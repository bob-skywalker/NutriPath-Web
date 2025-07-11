<script setup>
const props = defineProps({
    meal: {
        type: Object,
        required: true
    }
})

const emit = defineEmits(['meal-logged', 'delete-meal'])

function logMeal() {
    emit('meal-logged', props.meal)
}

function deleteMeal(event) {
    // Stop the click from propagating to the card (which would log the meal)
    event.stopPropagation()
    emit('delete-meal', props.meal.id)
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
          relative
        "
        @click="logMeal"
    >
        <!-- 🗑️ Delete Button -->
        <button
            @click="deleteMeal"
            class="
              absolute top-0 -right-1
              w-4 h-4 
              bg-red-500 hover:bg-red-600 
              text-white text-xs font-bold
              rounded-full
              flex items-center justify-center
              transition-all duration-200
              hover:scale-110
              focus:outline-none focus:ring-2 focus:ring-red-300
              sm:w-5 sm:h-5
              md:w-5 md:h-5 md:text-xs
            "
            title="Delete meal"
        >
            ×
        </button>

        <span class="
          font-medium text-gray-800
          sm:text-base
          md:text-lg
          pr-8
        ">
          {{ meal.name }}
        </span>
            <span class="
      text-xs text-gray-600 bg-white px-2 py-1 rounded-full
      sm:text-sm sm:bg-transparent sm:px-0 sm:py-0 sm:rounded-none
      md:text-base
    "> 
      {{ meal.calories || (meal.protein * 4 + meal.carbs * 4 + meal.fats * 9) }} kcal
    </span>
    </div>
</template>