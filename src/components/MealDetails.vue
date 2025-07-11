<script setup>
import MacroChart from './MacroChart.vue'

const props = defineProps({
    meal: {
        type: Object,
        required: true
    }
})

// 🎯 Define emits to pass save event up to parent
const emit = defineEmits(['save-meal'])

function handleSaveMeal(mealData) {
    emit('save-meal', mealData)
}
</script>

<template>
    <div class="mt-8 p-8 bg-gradient-to-br from-white to-gray-50 rounded-2xl shadow-xl border border-gray-100">
        <h3 class="text-2xl font-bold mb-6 text-gray-800 text-center">
            ✨ Last Logged Meal
        </h3>
        
        <!-- 🎯 Use the FANCY chart -->
        <MacroChart 
            :meal="meal" 
            size="fancy"
            theme="fancy"
            :show-legend="true"
            @save-meal="handleSaveMeal"
        />
    </div>
</template>