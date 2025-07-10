<script setup>
import { ref } from 'vue'

const mealName = ref('')
const calories = ref('')

const emit = defineEmits(['add-meal'])

function addMeal() {
    if (mealName.value.trim() && calories.value ) {
        const newMeal = {
            name: mealName.value.trim(),
            calories: parseInt(calories.value, 10)
        }

        emit('add-meal', newMeal)

        mealName.value = ''
        calories.value = ''
    }
}

function handleKeypress(event) {
    if (event.key === 'Enter') {
        addMeal()
    }
}
</script>

<template>
    <!-- 🎯 Responsive Add Meal Form -->
    <div class="
      mt-6 
      flex flex-col gap-3
      sm:flex-row sm:gap-3
      md:mt-8
    ">
        <input
            v-model="mealName"
            @keypress="handleKeypress"
            class="
              flex-1 p-3 border border-gray-300 rounded-lg 
              focus:outline-none focus:ring-2 focus:ring-green-500 
              text-black
              sm:text-sm
              md:p-4 md:text-base
            "
            placeholder="New meal name"
        />
        <input
            v-model="calories"
            @keypress="handleKeypress"
            class="
              w-full p-3 border border-gray-300 rounded-lg 
              focus:outline-none focus:ring-2 focus:ring-green-500 
              text-black
              sm:w-24 sm:text-sm
              md:w-32 md:p-4 md:text-base
            "
            placeholder="Calories"
            type="number"
        >
        <button
            @click="addMeal"
            class="
              px-6 py-3 bg-green-600 text-white rounded-lg 
              hover:bg-green-700 transition-colors
              sm:px-4 sm:py-3
              md:px-8 md:py-4 md:text-lg
              lg:px-10
            "
        >
            Add
        </button>
    </div>
</template>