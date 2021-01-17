<template>
    <p
        :class="getClass"
    >
        <slot />
    </p>
</template>

<script>
export default {
    props: {
        success: {
            type: Boolean,
            required: true,
        },
    },
    computed: {
        getClass() {
            return this.success ? 'success' : 'error';
        }
    }
}
</script>
