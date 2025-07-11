<script setup>
import MacroChart from './MacroChart.vue'

// Example meals showing both methods
const caloriesOnlyMeal = {
  name: 'Old Style Meal',
  calories: 450
}

const macrosDirectMeal = {
  name: 'Grilled Chicken Bowl',
  protein: 32,
  carbs: 25,
  fats: 14
}

const hybridMeal = {
  name: 'Mixed Data Meal',
  calories: 380,
  protein: 28,
  carbs: 35,
  fats: 12
}
</script>

<template>
  <div class="space-y-8 p-6 bg-gray-50 rounded-xl">
    <h2 class="text-2xl font-bold text-center text-gray-800">
      🎯 MacroChart Usage Examples
    </h2>
    
    <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
      <!-- Method 1: Calories Only (Old Way) -->
      <div class="bg-white p-4 rounded-lg shadow">
        <h3 class="text-lg font-semibold mb-4 text-center">Calories Only</h3>
        <MacroChart 
          :meal="caloriesOnlyMeal" 
          size="medium"
          :show-save-button="false"
        />
        <div class="mt-4 text-sm text-gray-600">
          <strong>Usage:</strong><br>
          <code>:meal="{ name: 'Meal', calories: 450 }"</code>
        </div>
      </div>
      
      <!-- Method 2: Direct Macros (New Way) -->
      <div class="bg-white p-4 rounded-lg shadow">
        <h3 class="text-lg font-semibold mb-4 text-center">Direct Macros</h3>
        <MacroChart 
          :meal="macrosDirectMeal" 
          size="medium"
          :show-save-button="false"
        />
        <div class="mt-4 text-sm text-gray-600">
          <strong>Usage:</strong><br>
          <code>:meal="{ name: 'Meal', protein: 32, carbs: 25, fats: 14 }"</code>
        </div>
      </div>
      
      <!-- Method 3: Props Override -->
      <div class="bg-white p-4 rounded-lg shadow">
        <h3 class="text-lg font-semibold mb-4 text-center">Props Override</h3>
        <MacroChart 
          :meal="{ name: 'Override Example' }" 
          :protein-grams="30"
          :carbs-grams="40"
          :fats-grams="15"
          size="medium"
          :show-save-button="false"
        />
        <div class="mt-4 text-sm text-gray-600">
          <strong>Usage:</strong><br>
          <code>:protein-grams="30" :carbs-grams="40" :fats-grams="15"</code>
        </div>
      </div>
    </div>
    
    <!-- Real Meal Examples -->
    <div class="mt-8">
      <h3 class="text-xl font-semibold mb-4 text-center">🍽️ Real Meal Examples</h3>
      <div class="grid grid-cols-2 md:grid-cols-4 gap-4 text-sm">
        <div class="bg-white p-3 rounded-lg">
          <strong>🥑 Avocado Toast</strong><br>
          P: 12g | C: 32g | F: 18g<br>
          <em>296 calories</em>
        </div>
        <div class="bg-white p-3 rounded-lg">
          <strong>🍗 Chicken Salad</strong><br>
          P: 35g | C: 8g | F: 15g<br>
          <em>307 calories</em>
        </div>
        <div class="bg-white p-3 rounded-lg">
          <strong>🐟 Salmon & Quinoa</strong><br>
          P: 40g | C: 45g | F: 22g<br>
          <em>538 calories</em>
        </div>
        <div class="bg-white p-3 rounded-lg">
          <strong>🥗 Buddha Bowl</strong><br>
          P: 16g | C: 65g | F: 20g<br>
          <em>484 calories</em>
        </div>
      </div>
    </div>
  </div>
</template> 