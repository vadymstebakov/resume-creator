<template>
    <div class="container">
        <p>
            <app-button
                v-if="comments.length"
                :modifier="danger"
                @action="closeComments"
            >
                Закрыть комментарии
            </app-button>
            <app-button
                v-else
                :modifier="primary"
                @action="loadComments"
            >
                Загрузить комментарии
            </app-button>
        </p>
        <div class="card" v-if="checkComments">
            <h2>Комментарии</h2>
            <ul class="list">
                <comments-list-item v-for="comment in comments" :key="comment.id" :comment="comment"></comments-list-item>
            </ul>
            <div class="center-content" v-if="!wasLoadAllComments">
                <app-button
                    @action="loadMoreComments"
                >
                    Загрузить больше комментариев
                </app-button>
            </div>
        </div>
        <loader v-else-if="loading"></loader>
    </div>
</template>
<script>
    import axios from 'axios';
    import { COMMENTS_URL } from './../../helpers/constants';
    import AppButton from './..//UI/AppButton';
    import Loader from './../UI/Loader';
    import CommentsListItem from './CommentsListItem'

    export default {
        data() {
            return {
                allComments: [],
                comments: [],
                commentsLength: 0,
                visibleCount: 5,
                visibleCommentsAmount: 5,
                loading: false,
            };
        },
        methods: {
            async loadComments() {
                try {
                    this.loading = true;
                    const { data } = await axios.get(COMMENTS_URL);
                    this.allComments = data;
                    this.commentsLength = data.length;
                    this.comments = this.allComments.slice(0, this.visibleCommentsAmount);
                    this.loading = false;
                } catch(error) {
                    this.loading = false;
                    console.error(error.message);
                }
            },
            loadMoreComments() {
                this.visibleCommentsAmount = this.visibleCommentsAmount + this.visibleCount;
                this.comments = this.allComments.slice(0, this.visibleCommentsAmount);

                if (this.wasLoadAllComments) {
                    this.allComments = [];
                }
            },
            closeComments() {
                this.comments = [];
                this.commentsLength = 0;
                this.visibleCommentsAmount = 5;
            }
        },
        computed: {
            checkComments() {
                return this.comments.length !== 0;
            },
            wasLoadAllComments() {
                return this.commentsLength === this.comments.length;
            }
        },
        inject: ['primary', 'danger', 'warning'],
        components: {
            AppButton,
            Loader,
            CommentsListItem,
        },
    };
</script>
<style scoped>

</style>
