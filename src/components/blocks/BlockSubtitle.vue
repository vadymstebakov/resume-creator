<template>
    <h2>{{ subtitle }}</h2>
</template>

<script>
export default {
    props: {
        subtitle: {
            type: String,
            required: true,
        },
    },
}
</script>
