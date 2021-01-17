<template>
    <div class="container column">
        <form class="card card-w30 card-min-h-250">
            <app-select
                labelValue="Тип блока"
                id="type"
                :options="selectOptions"
                v-model:selectVal="selectedBlockType"
            ></app-select>
            <div class="form-control">
                <app-textarea
                    v-if="!isSelectAvatar"
                    labelValue="Значение"
                    for="value"
                    id="value"
                    v-model:textareaVal.trim="value"
                ></app-textarea>
                <app-input-type-file
                    v-else
                    accept=".jpg, .jpeg, .png"
                    :fileError="validation.file"
                    @action="uploadImage"
                ></app-input-type-file>
            </div>
            <p>
                <app-button
                    :modifier="primary"
                    :isDisable="isDisableBtnAdd"
                    @action="addBlock"
                >
                    Добавить
                </app-button>
            </p>
            <p>
                <app-button
                    :modifier="warning"
                    :isDisable="isDisableBtnSend"
                    @action="sendBlocks"
                >
                    Сохранить резюме
                </app-button>
            </p>
            <app-tip
                v-if="hasTipText"
                :success="success"
            >
                {{ tipText }}
            </app-tip>
        </form>
        <div v-if="loaded" class="center-content w-70">
            <app-loader></app-loader>
        </div>
        <div v-else class="card card-w70 card-min-h-250">
            <h3 v-if="!isCreatedBlocks">Добавьте первый блок, чтобы увидеть результат</h3>
            <template v-else>
                <component v-for="block in blocks" :key="block.id" :is="getBlockComponentName(block)" v-bind="getBlockComponentProps(block)"></component>
            </template>
        </div>
    </div>
    <comments-list :primary="primary" :danger="danger"></comments-list>
</template>

<script>
import axios from 'axios';
import { PRIMARY, DANGER, WARNING, AVATAR_SIZE, AVATAR, TITLE, SUBTITLE, TEXT, RESUME_BLOCKS_URL } from './helpers/constants';
import AppButton from './components/UI/AppButton';
import AppTextarea from './components/UI/AppTextarea';
import AppInputTypeFile from './components/UI/AppInputTypeFile';
import AppSelect from './components/UI/AppSelect';
import AppTip from './components/UI/AppTip';
import CommentsList from './components/comments/CommentsList';
import BlockTitle from './components/blocks/BlockTitle';
import BlockAvatar from './components/blocks/BlockAvatar';
import BlockSubtitle from './components/blocks/BlockSubtitle';
import BlockText from './components/blocks/BlockText';
import AppLoader from './components/UI/AppLoader';

export default {
    data() {
        return {
            primary: PRIMARY,
            danger: DANGER,
            warning: WARNING,
            validation: {
                file: '',
            },
            value: '',
            inputAvatar: null,
            isCreateBlock: false,
            selectedBlockType: TITLE,
            selectOptions: [
                {
                    value: TITLE,
                    text: 'Заголовок',
                    defaultOption: false,
                    id: 1,
                },
                {
                    value: SUBTITLE,
                    text: 'Подзаголовок',
                    defaultOption: false,
                    id: 2,
                },
                {
                    value: AVATAR,
                    text: 'Аватар',
                    defaultOption: false,
                    id: 3,
                },
                {
                    value: TEXT,
                    text: 'Текст',
                    defaultOption: false,
                    id: 4,
                },
            ],
            blocks: [],
            prevBlocksLength: 0,
            disabledBtnSend: false,
            loaded: false,
            success: false,
            tipText: '',
        };
    },
    mounted() {
        this.getBlocks();
    },
    methods: {
        resetForm() {
            if (this.inputAvatar) {
                this.inputAvatar.value = '';
                this.inputAvatar = null;
            }

            this.value = '';
        },
        addBlock() {
            this.blocks.push({
                type: this.selectedBlockType,
                value: this.value,
                id: `_${Date.now()}`,
            });

            this.resetForm();
            this.selectedBlockType = TITLE;
            this.isCreateBlock = true;
        },
        getBlockComponentName(block) {
            return `block-${block.type}`;
        },
        getBlockComponentProps(block) {
            return {
                value: block.value,
            };
        },
        uploadImage(e) {
            const input = e.target;
            const file = input.files[0];
            this.validation.file = '';

            if (file.size > AVATAR_SIZE) {
                this.validation.file = 'Размер фото не должен превышать 1Mb';
                this.value = input.value = '';
                return;
            }

            const reader = new FileReader();

            reader.onloadend = () => {
                this.value = reader.result;
                this.inputAvatar = input;
            };
            reader.onerror = () => {
                console.error('Reader was not load');
            };
            reader.readAsDataURL(file);
        },
        resetTip() {
            if (this.hasTipText) {
                this.success = false;
                this.tipText = '';
            }
        },
        async sendBlocks() {
            try {
                this.disabledBtnSend = true;

                const response = await axios.post(RESUME_BLOCKS_URL, {
                    data: this.blocks[this.prevBlocksLength],
                });

                if (response.statusText !== 'OK') {
                    throw Error('Something went wrong');
                }

                this.disabledBtnSend = false;
                this.prevBlocksLength = this.blocks.length;
                this.success = true;
                this.tipText = 'Резюме сохранено успешно';
            } catch(error) {
                this.disabledBtnSend = false;
                this.success = false;
                this.tipText = 'Произошла ошибка при загрузке резюме';
                console.error(error.message);
            }
        },
        async getBlocks() {
            try {
                this.loaded = true;
                const { data } = await axios.get(RESUME_BLOCKS_URL);

                if (!data) {
                    this.loaded = false;
                   return;
                }

                this.blocks = Object.keys(data).reduce((acc, item) => {
                    acc.push(data[item].data);
                    return acc;
                }, []);
                this.prevBlocksLength = this.blocks.length;
                this.loaded = false;
            } catch(error) {
                this.loaded = false;
                console.error(error.message);
            }
        },
    },
    computed: {
        isDisableBtnAdd() {
            return this.value.length < 3 && !this.isSelectAvatar || this.value.length === 0 && this.isSelectAvatar;
        },
        isDisableBtnSend() {
            return this.disabledBtnSend || this.blocks.length === 0 || this.prevBlocksLength === this.blocks.length;
        },
        isSelectAvatar() {
            return this.selectedBlockType === AVATAR;
        },
        isCreatedBlocks() {
            return this.blocks.length > 0;
        },
        hasTipText() {
            return this.tipText.length > 0;
        }
    },
    watch: {
        selectedBlockType() {
            this.resetForm();
            this.resetTip();
            this.validation.file = '';
        },
        value() {
            this.resetTip();
        },
    },
    components: {
        AppButton,
        AppTextarea,
        AppInputTypeFile,
        AppSelect,
        AppTip,
        CommentsList,
        BlockTitle,
        BlockAvatar,
        BlockSubtitle,
        BlockText,
        AppLoader,
    },
};
</script>
