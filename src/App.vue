<script setup>
import { ref } from 'vue';
import AppHeader from './components/AppHeader.vue';
import MealCard from './components/MealCard.vue'
import AddMealForm from './components/AddMealForm.vue';
import MealDetails from './components/MealDetails.vue';
import ToastNotification from './components/ToastNotification.vue';
import SavedMealCard from './components/SavedMealCard.vue';

// 🎯 Ref to AddMealForm component
const addMealFormRef = ref(null)

const lastLoggedMeal = ref(null)
const toastMessage = ref('')
const showToast = ref(false)

const meals = ref([
  // 🥑 Existing meals with real macro data
  { id: 1, name: 'Avocado Toast', protein: 12, carbs: 32, fats: 18 },
  { id: 2, name: 'Chicken Salad', protein: 35, carbs: 8, fats: 15 },
  { id: 3, name: 'Smoothie Bowl', protein: 8, carbs: 45, fats: 12 },
  
  // 🍳 9 New meal examples with realistic nutrition data
  { id: 4, name: 'Greek Yogurt Parfait', protein: 20, carbs: 35, fats: 5 },
  { id: 5, name: 'Salmon & Quinoa', protein: 40, carbs: 45, fats: 22 },
  { id: 6, name: 'Turkey Wrap', protein: 25, carbs: 30, fats: 12 },
  { id: 7, name: 'Veggie Omelet', protein: 18, carbs: 6, fats: 16 },
  { id: 8, name: 'Protein Pancakes', protein: 24, carbs: 28, fats: 8 },
  { id: 9, name: 'Tuna Poke Bowl', protein: 30, carbs: 55, fats: 8 },
  { id: 10, name: 'Steak & Sweet Potato', protein: 35, carbs: 40, fats: 18 },
  { id: 11, name: 'Protein Smoothie', protein: 32, carbs: 15, fats: 6 },
  { id: 12, name: 'Buddha Bowl', protein: 16, carbs: 65, fats: 20 }
])

// 🍽️ Saved meals with macro breakdown
const savedMeals = ref([])

// 🍽️ Load saved meals from localStorage on startup
if (typeof window !== 'undefined') {
  const saved = localStorage.getItem('nutritrack-saved-meals')
  if (saved) {
    try {
      savedMeals.value = JSON.parse(saved)
    } catch (e) {
      console.log('Could not load saved meals:', e)
    }
  }
}

function handleMealLog(meal){
  lastLoggedMeal.value = meal
  showToastMessage(`${meal.name} logged (${meal.calories} kcal)`)
}

function handleAddMeal(newMeal) {
  const id = Date.now()
  meals.value.push({ ...newMeal, id })
  showToastMessage(`${newMeal.name} added`)
}

function showToastMessage(message) {
  toastMessage.value = message
  showToast.value = true 
  setTimeout( ()=> {
    showToast.value = false 
  }, 3000)
}

// 🍽️ Save meal to collection
function handleSaveMeal(mealData) {
  // Check if meal already exists
  const existingIndex = savedMeals.value.findIndex(saved => saved.name === mealData.name)
  
  if (existingIndex !== -1) {
    // Update existing meal
    savedMeals.value[existingIndex] = mealData
    showToastMessage(`Updated ${mealData.name} in your meal list! 📝`)
  } else {
    // Add new meal
    savedMeals.value.push(mealData)
    showToastMessage(`Saved ${mealData.name} to your meal list! 🍽️`)
  }
  
  // Persist to localStorage
  if (typeof window !== 'undefined') {
    localStorage.setItem('nutritrack-saved-meals', JSON.stringify(savedMeals.value))
  }
  
  // 🎯 Collapse the AddMealForm after saving
  if (addMealFormRef.value) {
    addMealFormRef.value.handleMealSaved()
  }
}

// 🍽️ Log a saved meal
function handleLogSavedMeal(savedMeal) {
  // Convert saved meal back to basic meal format for logging
  const basicMeal = {
    id: savedMeal.id,
    name: savedMeal.name,
    calories: savedMeal.calories,
    // Include the exact macro breakdown from saved meal
    protein: savedMeal.macros.protein.grams,
    carbs: savedMeal.macros.carbs.grams,
    fats: savedMeal.macros.fats.grams
  }
  
  handleMealLog(basicMeal)
}

// 🗑️ Delete a saved meal
function handleDeleteSavedMeal(mealId) {
  const mealIndex = savedMeals.value.findIndex(meal => meal.id === mealId)
  if (mealIndex !== -1) {
    const deletedMeal = savedMeals.value[mealIndex]
    savedMeals.value.splice(mealIndex, 1)
    
    // Persist to localStorage
    if (typeof window !== 'undefined') {
      localStorage.setItem('nutritrack-saved-meals', JSON.stringify(savedMeals.value))
    }
    
    showToastMessage(`Deleted ${deletedMeal.name} from your meal list 🗑️`)
  }
}

// 🗑️ Delete a main meal
function handleDeleteMeal(mealId) {
  const mealIndex = meals.value.findIndex(meal => meal.id === mealId)
  if (mealIndex !== -1) {
    const deletedMeal = meals.value[mealIndex]
    meals.value.splice(mealIndex, 1)
    showToastMessage(`Deleted ${deletedMeal.name} from quick add meals ��️`)
  }
}
</script>

<template>
  <div class="min-h-screen bg-green-50">
    <AppHeader />

    <!-- 🎯 Responsive Container -->
    <div class="
      w-full 
      min-h-screen
      p-4 
      sm:p-6 
      md:p-8 
      lg:p-10 
      xl:p-12
      bg-white
    ">
      <!-- 🎯 Responsive Heading -->
      <h1 class="
        text-xl font-bold text-center mb-4 text-gray-500
        sm:text-2xl sm:mb-6
        md:text-3xl md:mb-8
      ">
        Quick Add Meal Tracker
      </h1>
      
      <!-- 🎯 Responsive Meal Cards Grid -->
      <div class="
        space-y-3
        sm:space-y-4
        md:grid md:grid-cols-2 md:gap-4 md:space-y-0
        lg:grid-cols-3
      ">
        <MealCard 
          v-for="meal in meals"
          :key="meal.id"
          :meal="meal"
          @meal-logged="handleMealLog"
          @delete-meal="handleDeleteMeal"
        />
      </div>

      <AddMealForm ref="addMealFormRef" @add-meal="handleAddMeal"/>

      <MealDetails 
        v-if="lastLoggedMeal"
        :meal="lastLoggedMeal"
        @save-meal="handleSaveMeal"
      />

      <!-- 🍽️ Saved Meals Section -->
      <div v-if="savedMeals.length > 0" class="mt-12">
        <div class="flex justify-between items-center mb-6">
          <h2 class="text-2xl font-bold text-gray-800">
            📚 My Saved Meals
          </h2>
          <div class="text-sm text-gray-500">
            {{ savedMeals.length }} saved meal{{ savedMeals.length !== 1 ? 's' : '' }}
          </div>
        </div>
        
        <div class="
          grid gap-4
          sm:grid-cols-1
          md:grid-cols-2 
          lg:grid-cols-3
          xl:grid-cols-4
        ">
          <SavedMealCard
            v-for="savedMeal in savedMeals"
            :key="savedMeal.id"
            :meal="savedMeal"
            @log-meal="handleLogSavedMeal"
            @delete-meal="handleDeleteSavedMeal"
          />
        </div>
      </div>
    </div>

    <ToastNotification
      :message="toastMessage"
      :show="showToast"
    />
  </div>
</template>

