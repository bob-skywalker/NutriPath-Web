<script setup>
import { computed } from 'vue';

const props = defineProps({
    meal: {
        type: Object,
        required: true
    }
})

const fats = computed(() => Math.round(props.meal.calories * 0.3 / 9))
const carbs = computed(() => Math.round(props.meal.calories * 0.4 / 4))
const protein = computed(() => Math.round(props.meal.calories * 0.3 / 4))
</script>

<template>
    <div class="mt-6 p-4 bg-gray-50 rounded-lg">
        <h3 class="text-lg font-semibold mb-3 text-black">Last Logged Meal</h3>
        <div class="space-y-2 text-gray-500 font-bold">
            <div class="font-medium">{{ meal.name }}</div>
            <div class="text-sm text-gray-600">Calories: {{ meal.calories }}</div>
            <div class="text-sm text-gray-600">Fats: {{ fats }}g</div>
            <div class="text-sm text-gray-600">Carbs: {{ carbs }}g</div>
            <div class="text-sm text-gray-600">Protein: {{ protein }}g</div>
        </div>
    </div>
</template>