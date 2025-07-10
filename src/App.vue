<script setup>
import { ref } from 'vue';
import AppHeader from './components/AppHeader.vue';
import MealCard from './components/MealCard.vue'
import AddMealForm from './components/AddMealForm.vue';
import MealDetails from './components/MealDetails.vue';
import ToastNotification from './components/ToastNotification.vue';

const lastLoggedMeal = ref(null)
const toastMessage = ref('')
const showToast = ref(false)

const meals = ref([
  { id: 1, name: 'Avocado Toast', calories: 320},
  { id: 2, name: 'Chicken Salad', calories: 350},
  { id: 3, name: 'Smoothie Bowl', calories: 290}
])

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
        />
      </div>

      <AddMealForm @add-meal="handleAddMeal"/>

      <MealDetails 
        v-if="lastLoggedMeal"
        :meal="lastLoggedMeal"
      />
    </div>

    <ToastNotification
      :message="toastMessage"
      :show="showToast"
    />
  </div>
</template>

