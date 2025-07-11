<script setup>
import { ref, computed } from 'vue'

const mealName = ref('')
const calories = ref('')

// 🎯 New: Detailed nutrition mode
const knowsNutrition = ref(false)
const protein = ref('')
const carbs = ref('')
const fats = ref('')

// 🎯 Collapse functionality
const isCollapsed = ref(false)

const emit = defineEmits(['add-meal', 'meal-saved'])

// 🎯 Validation for both modes
const isValidSimple = computed(() => 
  mealName.value.trim() && calories.value
)

const isValidDetailed = computed(() => 
  mealName.value.trim() && protein.value && carbs.value && fats.value
)

const canSubmit = computed(() => 
  knowsNutrition.value ? isValidDetailed.value : isValidSimple.value
)

function addMeal() {
    if (!canSubmit.value) return

    const newMeal = {
        name: mealName.value.trim(),
    }

    if (knowsNutrition.value) {
        // 🎯 Detailed nutrition mode
        newMeal.protein = parseInt(protein.value, 10)
        newMeal.carbs = parseInt(carbs.value, 10)
        newMeal.fats = parseInt(fats.value, 10)
        // Calories will be calculated automatically by MacroChart
    } else {
        // 🎯 Simple calories mode
        newMeal.calories = parseInt(calories.value, 10)
    }

    emit('add-meal', newMeal)
    clearForm()
    // 🎯 Auto-collapse after adding meal
    setTimeout(() => {
        isCollapsed.value = true
    }, 500)
}

function clearForm() {
    mealName.value = ''
    calories.value = ''
    protein.value = ''
    carbs.value = ''
    fats.value = ''
}

function handleKeypress(event) {
    if (event.key === 'Enter') {
        addMeal()
    }
}

// 🎯 Toggle between modes
function toggleNutritionMode() {
    knowsNutrition.value = !knowsNutrition.value
    // Clear opposite mode fields when switching
    if (knowsNutrition.value) {
        calories.value = ''
    } else {
        protein.value = ''
        carbs.value = ''
        fats.value = ''
    }
}

// 🎯 Collapse/Expand functionality
function toggleCollapse() {
    isCollapsed.value = !isCollapsed.value
}

// 🎯 Handle meal saved from external source (like MacroChart save button)
function handleMealSaved() {
    setTimeout(() => {
        isCollapsed.value = true
    }, 800)
}

// 🎯 Expose the collapse function for parent components
defineExpose({
    collapse: () => isCollapsed.value = true,
    expand: () => isCollapsed.value = false,
    handleMealSaved
})
</script>

<template>
    <!-- 🎯 Enhanced Collapsible Add Meal Form -->
    <div class="mt-6 md:mt-8">
        <!-- 🎯 Always Visible Header with Collapse Toggle -->
        <div class="mb-4 p-4 bg-gradient-to-r from-blue-50 to-green-50 rounded-xl border border-gray-200 cursor-pointer hover:from-blue-100 hover:to-green-100 transition-colors"
             @click="toggleCollapse">
            <div class="flex items-center justify-between">
                <div class="flex items-center space-x-3">
                    <div class="flex items-center space-x-2">
                        <h3 class="font-semibold text-gray-800">Add New Meal</h3>
                        <div 
                            :class="[
                                'transform transition-transform duration-200',
                                isCollapsed ? 'rotate-0' : 'rotate-180'
                            ]"
                        >
                            <svg class="w-5 h-5 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"/>
                            </svg>
                        </div>
                    </div>
                    <div v-if="isCollapsed" class="text-xs text-gray-500 bg-white px-2 py-1 rounded-full">
                        {{ knowsNutrition ? 'Detailed Mode' : 'Simple Mode' }}
                    </div>
                </div>
                <div v-if="!isCollapsed" class="flex items-center space-x-2">
                    <button
                        @click.stop="toggleNutritionMode"
                        class="relative inline-flex items-center space-x-2 bg-white p-2 rounded-lg border border-gray-300 hover:border-gray-400 transition-colors"
                    >
                        <span class="text-sm font-medium text-gray-700">
                            {{ knowsNutrition ? 'Detailed' : 'Simple' }}
                        </span>
                        <div 
                            :class="[
                                'relative inline-block w-12 h-6 rounded-full transition-colors duration-200',
                                knowsNutrition ? 'bg-green-500' : 'bg-gray-300'
                            ]"
                        >
                            <div 
                                :class="[
                                    'absolute top-0.5 left-0.5 w-5 h-5 bg-white rounded-full shadow transform transition-transform duration-200',
                                    knowsNutrition ? 'translate-x-6' : 'translate-x-0'
                                ]"
                            ></div>
                        </div>
                    </button>
                </div>
            </div>
            <p v-if="!isCollapsed" class="text-sm text-gray-600 mt-2">
                {{ knowsNutrition ? 'Enter detailed nutrition info' : 'Enter calories only' }}
            </p>
            <p v-else class="text-sm text-gray-500 mt-1">
                Click to expand form
            </p>
        </div>

        <!-- 🎯 Collapsible Form Content -->
        <div 
            :class="[
                'overflow-hidden transition-all duration-300 ease-in-out',
                isCollapsed ? 'max-h-0 opacity-0' : 'max-h-[1000px] opacity-100'
            ]"
        >
            <div class="space-y-4 pb-4">
                <!-- Meal Name -->
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                        Meal Name *
                    </label>
                    <input
                        v-model="mealName"
                        @keypress="handleKeypress"
                        class="
                          w-full p-3 border border-gray-300 rounded-lg 
                          focus:outline-none focus:ring-2 focus:ring-green-500 focus:border-transparent
                          text-black transition-colors
                          md:p-4 md:text-base
                        "
                        placeholder="e.g., Grilled Chicken Bowl"
                    />
                </div>

                <!-- 🎯 Simple Mode: Calories Only -->
                <div v-if="!knowsNutrition" class="transition-all duration-300">
                    <label class="block text-sm font-medium text-gray-700 mb-2">
                        Calories *
                    </label>
                    <input
                        v-model="calories"
                        @keypress="handleKeypress"
                        class="
                          w-full p-3 border border-gray-300 rounded-lg 
                          focus:outline-none focus:ring-2 focus:ring-green-500 focus:border-transparent
                          text-black transition-colors
                          md:p-4 md:text-base
                          sm:w-48
                        "
                        placeholder="e.g., 350"
                        type="number"
                        min="1"
                        max="9999"
                    />
                    <p class="text-xs text-gray-500 mt-1">
                        💡 Macros will be estimated automatically
                    </p>
                </div>

                <!-- 🎯 Detailed Mode: Individual Macros -->
                <div v-else class="transition-all duration-300">
                    <label class="block text-sm font-medium text-gray-700 mb-3">
                        Nutritional Information *
                    </label>
                    <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
                        <!-- Protein -->
                        <div>
                            <label class="block text-xs font-medium text-gray-600 mb-1">
                                🥩 Protein (g)
                            </label>
                            <input
                                v-model="protein"
                                @keypress="handleKeypress"
                                class="
                                  w-full p-3 border border-gray-300 rounded-lg 
                                  focus:outline-none focus:ring-2 focus:ring-red-500 focus:border-transparent
                                  text-black transition-colors
                                "
                                placeholder="25"
                                type="number"
                                min="0"
                                max="200"
                            />
                        </div>
                        
                        <!-- Carbs -->
                        <div>
                            <label class="block text-xs font-medium text-gray-600 mb-1">
                                🍞 Carbs (g)
                            </label>
                            <input
                                v-model="carbs"
                                @keypress="handleKeypress"
                                class="
                                  w-full p-3 border border-gray-300 rounded-lg 
                                  focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent
                                  text-black transition-colors
                                "
                                placeholder="30"
                                type="number"
                                min="0"
                                max="500"
                            />
                        </div>
                        
                        <!-- Fats -->
                        <div>
                            <label class="block text-xs font-medium text-gray-600 mb-1">
                                🥑 Fats (g)
                            </label>
                            <input
                                v-model="fats"
                                @keypress="handleKeypress"
                                class="
                                  w-full p-3 border border-gray-300 rounded-lg 
                                  focus:outline-none focus:ring-2 focus:ring-orange-500 focus:border-transparent
                                  text-black transition-colors
                                "
                                placeholder="12"
                                type="number"
                                min="0"
                                max="200"
                            />
                        </div>
                    </div>
                    <p class="text-xs text-gray-500 mt-2">
                        ⚡ Calories will be calculated automatically ({{ 
                            protein ? parseInt(protein) * 4 : 0 
                        }} + {{ 
                            carbs ? parseInt(carbs) * 4 : 0 
                        }} + {{ 
                            fats ? parseInt(fats) * 9 : 0 
                        }} = {{ 
                            (protein ? parseInt(protein) * 4 : 0) + 
                            (carbs ? parseInt(carbs) * 4 : 0) + 
                            (fats ? parseInt(fats) * 9 : 0) 
                        }} calories)
                    </p>
                </div>

                <!-- 🎯 Submit Button -->
                <button
                    @click="addMeal"
                    :disabled="!canSubmit"
                    :class="[
                        'w-full px-6 py-3 rounded-xl font-semibold transition-all duration-200 flex items-center justify-center space-x-2',
                        'md:py-4 md:text-lg',
                        canSubmit 
                            ? 'bg-gradient-to-r from-green-500 to-green-600 hover:from-green-600 hover:to-green-700 text-white shadow-lg hover:shadow-xl transform hover:scale-105' 
                            : 'bg-gray-300 text-gray-500 cursor-not-allowed'
                    ]"
                >
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6"/>
                    </svg>
                    <span>Add {{ knowsNutrition ? 'Detailed' : 'Simple' }} Meal</span>
                </button>

                <!-- 🎯 Help Text -->
                <div class="mt-4 p-3 bg-gray-50 rounded-lg">
                    <p class="text-xs text-gray-600">
                        <strong>💡 Tip:</strong> 
                        {{ knowsNutrition 
                            ? 'You can find nutrition info on food packaging or apps like MyFitnessPal' 
                            : 'Switch to detailed mode if you know the exact protein, carbs, and fats'
                        }}
                    </p>
                </div>
            </div>
        </div>
    </div>
</template>