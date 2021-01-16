<template>
    <div class="avatar">
        <img
            :src="avatar"
            alt=""
        />
    </div>
</template>

<script>
export default {
    props: {
        avatar: {
            type: String,
            required: true,
        },
    },
}
</script>
