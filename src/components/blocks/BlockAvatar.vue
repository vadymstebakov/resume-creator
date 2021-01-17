<template>
    <div class="avatar">
        <img
            :src="value"
            alt=""
        />
    </div>
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
