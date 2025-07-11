<script setup>
import MacroChart from './MacroChart.vue'

const sampleMeal = { 
  id: 1, 
  name: 'Premium Protein Bowl', 
  calories: 520 
}
</script>

<template>
  <div class="space-y-12 p-8">
    <h2 class="text-3xl font-bold text-center text-gray-800">
      🎨 Fancy Chart Showcase
    </h2>
    
    <!-- 🎯 Size Comparison -->
    <div class="grid grid-cols-1 lg:grid-cols-2 xl:grid-cols-3 gap-8">
      <div>
        <h3 class="text-xl font-semibold mb-4 text-center">Extra Large</h3>
        <MacroChart 
          :meal="sampleMeal" 
          size="xl"
          theme="default"
        />
      </div>
      
      <div>
        <h3 class="text-xl font-semibold mb-4 text-center">Fancy Theme</h3>
        <MacroChart 
          :meal="sampleMeal" 
          size="fancy"
          theme="fancy"
        />
      </div>
      
      <div>
        <h3 class="text-xl font-semibold mb-4 text-center">Neon Theme</h3>
        <MacroChart 
          :meal="sampleMeal" 
          size="xl"
          theme="neon"
        />
      </div>
    </div>
  </div>
</template>