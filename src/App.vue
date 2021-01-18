<template>
    <div class="container column">
        <form-view
            :blocks="blocks"
            :prevBlocksLength="prevBlocksLength"
            :primary="primary"
            :warning="warning"
            @add-block="addBlock"
            @send-blocks="sendBlocks"
        ></form-view>
        <resume-view
            :blocks="blocks"
            @get-blocks="getBlocks"
        >
        </resume-view>
    </div>
    <comments-list :primary="primary" :danger="danger"></comments-list>
</template>

<script>
import { PRIMARY, DANGER, WARNING } from './helpers/constants';
import FormView from './components/form/FormView';
import ResumeView from './components/resume/ResumeView';
import CommentsList from './components/comments/CommentsList';
export default {
    data() {
        return {
            primary: PRIMARY,
            danger: DANGER,
            warning: WARNING,
            blocks: [],
            prevBlocksLength: 0,
        };
    },
    methods: {
        addBlock(blocks) {
            this.blocks.push(blocks);
        },
        sendBlocks(blocksLength) {
            this.prevBlocksLength = blocksLength;
        },
        getBlocks(data) {
            this.blocks = Object.keys(data).reduce((acc, item) => {
                acc.push(data[item].data);
                return acc;
            }, []);
            this.prevBlocksLength = this.blocks.length;
        },
    },
    components: {
        FormView,
        ResumeView,
        CommentsList,
    },
};
</script>
