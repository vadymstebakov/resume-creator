<template>
    <label :for="id"> {{ labelValue }} </label>
    <textarea
        v-bind="$attrs"
        :id="id"
        :rows="rowsCount"
        :value="textareaVal"
        @input="handleTextarea"
    ></textarea>
</template>

<script>
export default {
    emits: {
        'update:textareaVal'(val) {
            if (typeof val === 'string') {
                return true;
            }

            console.warn('Value does not exist');
            return false;
        }
    },
    props: {
        labelValue: {
            type: String,
            required: true,
        },
        id: {
            type: String,
            required: true,
        },
        for: {
            type: String,
            required: true,
        },
        rowsCount: {
            type: Number,
            default: 3,
        },
        textareaVal: {
            type: String,
            default: '',
        }
    },
    methods: {
        handleTextarea(e) {
            this.$emit('update:textareaVal', e.target.value);
        },
    },
}
</script>
