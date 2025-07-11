<script setup>
import MacroChart from './MacroChart.vue'

// Example meals for demonstration
const sampleMeals = [
  { id: 1, name: 'Breakfast Bowl', calories: 450 },
  { id: 2, name: 'Protein Shake', calories: 280 },
  { id: 3, name: 'Chicken Salad', calories: 380 }
]
</script>

<template>
  <div class="space-y-8">
    <h2 class="text-2xl font-bold text-center text-gray-800">
      MacroChart Component Showcase
    </h2>
    
    <!-- 🎯 Different Sizes -->
    <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
      <div class="bg-white p-4 rounded-lg shadow-md">
        <h3 class="text-lg font-semibold mb-4 text-center">Small Size</h3>
        <MacroChart 
          :meal="sampleMeals[0]" 
          size="small"
          :show-legend="false"
        />
      </div>
      
      <div class="bg-white p-4 rounded-lg shadow-md">
        <h3 class="text-lg font-semibold mb-4 text-center">Medium Size</h3>
        <MacroChart 
          :meal="sampleMeals[1]" 
          size="medium"
          :show-legend="true"
        />
      </div>
      
      <div class="bg-white p-4 rounded-lg shadow-md">
        <h3 class="text-lg font-semibold mb-4 text-center">Large Size</h3>
        <MacroChart 
          :meal="sampleMeals[2]" 
          size="large"
          :show-legend="true"
        />
      </div>
    </div>
    
    <!-- 🎯 Horizontal Layout -->
    <div class="bg-white p-6 rounded-lg shadow-md">
      <h3 class="text-lg font-semibold mb-6 text-center">Horizontal Comparison</h3>
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        <MacroChart 
          v-for="meal in sampleMeals"
          :key="meal.id"
          :meal="meal" 
          size="medium"
          :show-legend="false"
        />
      </div>
    </div>
  </div>
</template> 