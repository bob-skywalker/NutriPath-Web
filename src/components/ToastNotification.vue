<script setup>
defineProps({
    message: {
        type: String,
        default: ''
    },
    show: {
        type: Boolean,
        default: false 
    }
})
</script>

<template>
    <div
        v-if="show"
        class="fixed bottom-5 left-1/2 transform -translate-x-1/2 text-white bg-green-600 px-5 py-3 rounded-lg shadow-lg z-50"
    >
        {{ message }}
    </div>
</template>