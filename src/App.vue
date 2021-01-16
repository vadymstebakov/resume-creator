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
            <h3>Добавьте первый блок, чтобы увидеть результат</h3>
        </div>
    </div>
    <comments-list :primary="primary" :danger="danger"></comments-list>
</template>

<script>
import { PRIMARY, DANGER, WARNING, AVATAR_SIZE } from './helpers/constants';
import AppButton from './components/UI/AppButton';
import AppTextarea from './components/UI/AppTextarea';
import AppInputTypeFile from './components/UI/AppInputTypeFile';
import AppSelect from './components/UI/AppSelect';
import CommentsList from './components/comments/CommentsList';

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
            selectedBlockType: 'title',
            selectOptions: [
                {
                    value: 'title',
                    text: 'Заголовок',
                    defaultOption: false,
                    id: 1,
                },
                {
                    value: 'subtitle',
                    text: 'Подзаголовок',
                    defaultOption: false,
                    id: 2,
                },
                {
                    value: 'avatar',
                    text: 'Аватар',
                    defaultOption: false,
                    id: 3,
                },
                {
                    value: 'text',
                    text: 'Текст',
                    defaultOption: false,
                    id: 4,
                },
            ],
            createdBlocks: {
                title: [],
                subtitle: [],
                avatar: [],
                text: [],
            },
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
            this.createdBlocks[this.selectedBlockType].push(this.value);

            this.resetForm();
            this.selectedBlockType = 'title';
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
        }
    },
    computed: {
        isDisable() {
            return this.value.length < 3 && !this.isSelectAvatar || this.value.length === 0 && this.isSelectAvatar;
        },
        isSelectAvatar() {
            return this.selectedBlockType === 'avatar';
        }
    },
    watch: {
        selectedBlockType() {
            this.resetForm();
            this.errors.file = '';
        }
    },
    components: {
        AppButton,
        AppTextarea,
        AppInputTypeFile,
        AppSelect,
        CommentsList,
    }
};
</script>
