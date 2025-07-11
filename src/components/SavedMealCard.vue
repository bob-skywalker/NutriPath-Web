<script setup>
const props = defineProps({
  meal: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['log-meal', 'delete-meal', 'edit-meal'])

function logMeal() {
  emit('log-meal', props.meal)
}

function deleteMeal() {
  emit('delete-meal', props.meal.id)
}

function editMeal() {
  emit('edit-meal', props.meal)
}
</script>

<template>
  <div class="bg-white p-5 rounded-xl shadow-md border border-gray-100 hover:shadow-lg transition-all duration-200 group">
    <!-- Header -->
    <div class="flex justify-between items-start mb-3">
      <h3 class="font-semibold text-gray-800 text-lg">{{ meal.name }}</h3>
      <button 
        @click="deleteMeal"
        class="opacity-0 group-hover:opacity-100 text-red-400 hover:text-red-600 transition-all p-1"
        title="Delete meal"
      >
        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
        </svg>
      </button>
    </div>

    <!-- Calories -->
    <div class="text-center mb-4 p-3 bg-gray-50 rounded-lg">
      <div class="text-2xl font-bold text-gray-800">{{ meal.calories }}</div>
      <div class="text-xs text-gray-500 uppercase tracking-wide">Calories</div>
    </div>

    <!-- Macro Breakdown -->
    <div class="space-y-2 mb-4">
      <div class="flex justify-between items-center">
        <div class="flex items-center space-x-2">
          <div class="w-3 h-3 bg-red-500 rounded-full"></div>
          <span class="text-sm text-gray-700">Protein</span>
        </div>
        <div class="text-right">
          <span class="font-medium text-gray-800">{{ meal.macros.protein.grams }}g</span>
          <div class="text-xs text-gray-500">{{ meal.macros.protein.percentage }}%</div>
        </div>
      </div>
      
      <div class="flex justify-between items-center">
        <div class="flex items-center space-x-2">
          <div class="w-3 h-3 bg-blue-500 rounded-full"></div>
          <span class="text-sm text-gray-700">Carbs</span>
        </div>
        <div class="text-right">
          <span class="font-medium text-gray-800">{{ meal.macros.carbs.grams }}g</span>
          <div class="text-xs text-gray-500">{{ meal.macros.carbs.percentage }}%</div>
        </div>
      </div>
      
      <div class="flex justify-between items-center">
        <div class="flex items-center space-x-2">
          <div class="w-3 h-3 bg-orange-500 rounded-full"></div>
          <span class="text-sm text-gray-700">Fats</span>
        </div>
        <div class="text-right">
          <span class="font-medium text-gray-800">{{ meal.macros.fats.grams }}g</span>
          <div class="text-xs text-gray-500">{{ meal.macros.fats.percentage }}%</div>
        </div>
      </div>
    </div>

    <!-- Action Button -->
    <button
      @click="logMeal"
      class="w-full bg-gradient-to-r from-blue-500 to-blue-600 hover:from-blue-600 hover:to-blue-700 text-white font-medium py-2 px-4 rounded-lg transition-all duration-200 transform hover:scale-105 flex items-center justify-center space-x-2"
    >
      <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"/>
      </svg>
      <span>Log This Meal</span>
    </button>

    <!-- Save Date -->
    <div class="text-xs text-gray-400 text-center mt-3">
      Saved: {{ new Date(meal.savedAt).toLocaleDateString() }}
    </div>
  </div>
</template> 