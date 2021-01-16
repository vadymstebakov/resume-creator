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
                    :fileError="errors.file"
                    @action="uploadImage"
                ></app-input-type-file>
            </div>
            <app-button
                :modifier="primary"
                :isDisable="isDisable"
                @action="addBlock"
            >
                Добавить
            </app-button>
        </form>
        <div class="card card-w70 card-min-h-250">
            <h3 v-if="!isCreatedBlocks">Добавьте первый блок, чтобы увидеть результат</h3>
            <template v-else>
                <component v-for="block in blocks" :key="block.id" :is="`block-${block.type}`" v-bind="blockProps"></component>
            </template>
        </div>
    </div>
    <comments-list :primary="primary" :danger="danger"></comments-list>
</template>

<script>
import { PRIMARY, DANGER, WARNING, AVATAR_SIZE, AVATAR, TITLE, SUBTITLE, TEXT } from './helpers/constants';
import AppButton from './components/UI/AppButton';
import AppTextarea from './components/UI/AppTextarea';
import AppInputTypeFile from './components/UI/AppInputTypeFile';
import AppSelect from './components/UI/AppSelect';
import CommentsList from './components/comments/CommentsList';
import BlockTitle from './components/blocks/BlockTitle';
import BlockAvatar from './components/blocks/BlockAvatar';
import BlockSubtitle from './components/blocks/BlockSubtitle';
import BlockText from './components/blocks/BlockText';

export default {
    data() {
        return {
            primary: PRIMARY,
            danger: DANGER,
            warning: WARNING,
            errors: {
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
        };
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
            console.log(this.blocks);
        },
        uploadImage(e) {
            const input = e.target;
            const file = input.files[0];
            this.errors.file = '';

            if (file.size > AVATAR_SIZE) {
                this.errors.file = 'Размер фото не должен превышать 1Mb';
                this.value = input.value = '';
                return;
            }

            const reader = new FileReader();

            reader.onloadend = () => {
                this.value = reader.result;
                this.inputAvatar = input;
            };
            reader.onerror = () => {
                console.error('Reader was not load...');
            };
            reader.readAsDataURL(file);
        },
    },
    computed: {
        isDisable() {
            return this.value.length < 3 && !this.isSelectAvatar || this.value.length === 0 && this.isSelectAvatar;
        },
        isSelectAvatar() {
            return this.selectedBlockType === AVATAR;
        },
        isCreatedBlocks() {
            return this.blocks.length > 0;
        },
        blockProps() {
            return this.blocks.reduce((acc, item) => {
                console.log(item);
                switch(item.type) {
                    case TITLE:
                        acc.title = item.value;
                        break;
                    case SUBTITLE:
                        acc.subtitle = item.value;
                        break;
                    case TEXT:
                        acc.text = item.value;
                        break;
                    case AVATAR:
                        acc.avatar = item.value;
                        break;
                    default:
                        break;
                }

                return acc;
            }, {});
        },
    },
    watch: {
        selectedBlockType() {
            this.resetForm();
            this.errors.file = '';
        },
    },
    components: {
        AppButton,
        AppTextarea,
        AppInputTypeFile,
        AppSelect,
        CommentsList,
        BlockTitle,
        BlockAvatar,
        BlockSubtitle,
        BlockText,
    },
};
</script>
