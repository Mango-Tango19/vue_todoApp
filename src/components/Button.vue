<template>
    <button  @click="$emit('toggle-add-btn')" class="btn" :style="{background: color}">
        {{ text }}</button>
</template>

<script>
export default {
    name: 'Button', 
    props: {
        color: String,
        text: String
    }
}
</script>
