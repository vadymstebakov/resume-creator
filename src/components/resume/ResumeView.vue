<template>
    <div v-if="loaded" class="center-content w-70">
        <app-loader></app-loader>
    </div>
    <div v-else class="card card-w70 card-min-h-250">
        <h3 v-if="!isCreatedBlocks">Добавьте первый блок, чтобы увидеть результат</h3>
        <template v-else>
            <component v-for="block in blocks" :key="block.id" :is="getBlockComponentName(block)" v-bind="getBlockComponentProps(block)"></component>
        </template>
    </div>
</template>

<script>
import axios from 'axios';
import { RESUME_BLOCKS_URL } from './../../helpers/constants';
import BlockTitle from './../blocks/BlockTitle';
import BlockAvatar from './../blocks/BlockAvatar';
import BlockSubtitle from './../blocks/BlockSubtitle';
import BlockText from './../blocks/BlockText';
import AppLoader from './../UI/AppLoader';

export default {
    emits: {
        'get-blocks': null,
    },
    props: {
        blocks: {
            type: Array,
            required: true,
        },
    },
    data() {
        return {
            loaded: false,
        };
    },
    mounted() {
        this.getBlocks();
    },
    methods: {
        getBlockComponentName(block) {
            return `block-${block.type}`;
        },
        getBlockComponentProps(block) {
            return {
                value: block.value,
            };
        },
        async getBlocks() {
            try {
                this.loaded = true;
                const { data } = await axios.get(RESUME_BLOCKS_URL);

                if (!data) {
                    this.loaded = false;
                   return;
                }

                this.$emit('get-blocks', data);
                this.loaded = false;
            } catch(error) {
                this.loaded = false;
                console.error(error.message);
            }
        },
    },
    computed: {
        isCreatedBlocks() {
            return this.blocks.length > 0;
        },
    },
    components: {
        BlockTitle,
        BlockAvatar,
        BlockSubtitle,
        BlockText,
        AppLoader,
    },
}
</script>
