<template>
    <input
        type="file"
        :accept="accept"
        @change="$emit('action', $event)"
    >
    <small v-if="hasError">{{ fileError }}</small>
</template>

<script>
export default {
    emits: {
        action(event) {
            if (event?.target?.files?.length) {
                return true;
            }

            console.warn('Event does not exist!')
            return false;
        }
    },
    props: {
        accept: {
            type: String,
            default: '',
        },
        fileError: {
            type: String,
            default: ''
        }
    },
    computed: {
        hasError() {
            return this.fileError.length !== 0;
        }
    },
}
</script>
