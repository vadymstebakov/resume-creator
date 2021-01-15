<template>
    <button
        class="btn"
        :class="modifier"
        :disabled="isDisable"
        @click.prevent="$emit('action')"
    >
        <slot />
    </button>
</template>
<script>
    import { PRIMARY, DANGER, WARNING } from './../../helpers/constants';

    export default {
        props: {
            modifier: {
                type: String,
                default: '',
                validator(val) {
                    return ['', PRIMARY, DANGER, WARNING].includes(val);
                }
            },
            isDisable: {
                type: Boolean,
                default: false,
            },
        },
        emits: {
            action: null
        },
    }
</script>
<style scoped>

</style>
