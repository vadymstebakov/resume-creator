<template>
    <li class="list-item">
        <div>
            <p><strong>{{ comment.email }}</strong></p>
            <small>{{ comment.body }}</small>
        </div>
    </li>
</template>

<script>
export default {
    props: {
        comment: {
            type: Object,
            required: true,
        }
    }
}
</script>
