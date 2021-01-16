<template>
    <div class="form-control">
        <label for="id">{{ labelValue }}</label>
        <select
            id="id"
            :value="selectVal"
            @change="handleSelect"
        >
            <option
                v-for="option in options"
                :selected="option.defaultOption"
                :disabled="option.defaultOption"
                :value="option.value"
                :key="option.id"
            >
                {{ option.text }}
            </option>
        </select>
    </div>
</template>

<script>
export default {
    emits: {
        'update:selectVal'(val) {
            if (typeof val !== 'string') {
                return false;
            }

            return true;
        },
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
        selectVal: {
            type: String,
            required: true,
        },
        options: {
            type: Array,
            required: true,
            validator(data) {
                if (data.length <= 1) {
                    return false;
                }

                let isValidData = true;

                data.forEach(item => {
                    const keys = Object.keys(item);

                    if (!keys.includes('value') || !keys.includes('text') || !keys.includes('id') || !keys.includes('defaultOption')) {
                        isValidData = false;
                    }

                    switch(isValidData) {
                        case typeof item.value !== 'string' || item.value.length === 0:
                        case typeof item.text !== 'string' || item.text.length === 0:
                        case typeof item.id !== 'number':
                        case typeof item.defaultOption !== 'boolean':
                            isValidData = false;
                            break;
                        default:
                            break;
                    }
                });

                if (isValidData) {
                    return true;
                } else {
                    console.warn('Data is not correct!');
                    return false;
                }
            }
        }
    },
    methods: {
        handleSelect(e) {
            this.$emit('update:selectVal', e.target.value);
        }
    }
}
</script>
