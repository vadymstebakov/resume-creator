<template>
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
</template>

<script>
import axios from 'axios';
import { AVATAR_SIZE, AVATAR, TITLE, SUBTITLE, TEXT, RESUME_BLOCKS_URL } from './../../helpers/constants';
import AppButton from './..//UI/AppButton';
import AppTextarea from './../UI/AppTextarea';
import AppInputTypeFile from './../UI/AppInputTypeFile';
import AppSelect from './../UI/AppSelect';
import AppTip from './../UI/AppTip';

export default {
    emits: {
        'add-block': null,
        'send-blocks': null,
    },
    props: {
        blocks: {
            type: Array,
            required: true,
        },
        prevBlocksLength: {
            type: Number,
            required: true,
        },
        primary: {
            type: String,
        },
        warning: {
            type: String,
        },
    },
    data() {
        return {
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
            validation: {
                file: '',
            },
            value: '',
            inputAvatar: null,
            selectedBlockType: TITLE,
            disabledBtnSend: false,
            success: false,
            tipText: '',
        }
    },
    methods: {
        addBlock() {
            this.$emit('add-block', {
                type: this.selectedBlockType,
                value: this.value,
                id: `_${Date.now()}`,
            });

            this.resetForm();
            this.selectedBlockType = TITLE;
        },
        resetForm() {
            if (this.inputAvatar) {
                this.inputAvatar.value = '';
                this.inputAvatar = null;
            }

            this.value = '';
        },
        resetTip() {
            if (this.hasTipText) {
                this.success = false;
                this.tipText = '';
            }
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
                this.$emit('send-blocks', this.blocks.length);
                this.success = true;
                this.tipText = 'Резюме сохранено успешно';
            } catch(error) {
                this.disabledBtnSend = false;
                this.success = false;
                this.tipText = 'Произошла ошибка при загрузке резюме';
                console.error(error.message);
            }
        },
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
    computed: {
        isDisableBtnAdd() {
            return this.value.length < 3 && !this.isSelectAvatar || this.value.length === 0 && this.isSelectAvatar;
        },
        isDisableBtnSend() {
            console.log()
            return this.disabledBtnSend || this.blocks.length === 0 || this.prevBlocksLength === this.blocks.length;
        },
        isSelectAvatar() {
            return this.selectedBlockType === AVATAR;
        },
        hasTipText() {
            return this.tipText.length > 0;
        }
    },
    components: {
        AppButton,
        AppTextarea,
        AppInputTypeFile,
        AppSelect,
        AppTip,
    },
}
</script>
