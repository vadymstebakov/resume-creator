<template>
    <p>{{ value }}</p>
</template>

<script>
export default {
    props: {
        value: {
            type: String,
            required: true,
        },
    },
}
</script>
