<script setup>
const props = defineProps({
    meal: {
        type: Object,
        required: true
    }
})

const emit = defineEmits(['meal-logged'])

function logMeal() {
    emit('meal-logged', props.meal)
}
</script>

<template>
    <div
        class="
          bg-green-100 p-3 rounded-lg cursor-pointer 
          hover:bg-green-200 transition-colors 
          flex flex-col items-start gap-2
          sm:flex-row sm:justify-between sm:items-center sm:p-4
          md:p-5
          lg:hover:scale-105 lg:transform lg:transition-transform
        "
        @click="logMeal"
    >
        <span class="
          font-medium text-gray-800
          sm:text-base
          md:text-lg
        ">
          {{ meal.name }}
        </span>
        <span class="
          text-xs text-gray-600 bg-white px-2 py-1 rounded-full
          sm:text-sm sm:bg-transparent sm:px-0 sm:py-0 sm:rounded-none
          md:text-base
        "> 
          {{ meal.calories }} kcal
        </span>
    </div>
</template>