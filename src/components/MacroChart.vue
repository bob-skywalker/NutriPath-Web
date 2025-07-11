<script setup>
import { computed } from 'vue'
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  ArcElement
} from 'chart.js'
import { Doughnut } from 'vue-chartjs'

// Register Chart.js components
ChartJS.register(Title, Tooltip, Legend, ArcElement)

const props = defineProps({
  meal: {
    type: Object,
    required: true
  },
  size: {
    type: String,
    default: 'medium'
  },
  showLegend: {
    type: Boolean,
    default: true
  },
  theme: {
    type: String,
    default: 'default'
  },
  showSaveButton: {
    type: Boolean,
    default: true
  },
  // 🎯 Direct macro props (optional)
  proteinGrams: {
    type: Number,
    default: null
  },
  carbsGrams: {
    type: Number,
    default: null
  },
  fatsGrams: {
    type: Number,
    default: null
  }
})

// 🎯 Define emits for saving meal
const emit = defineEmits(['save-meal'])

// 🎲 Seeded random function for consistent randomization
const seededRandom = (seed) => {
  const x = Math.sin(seed) * 10000
  return x - Math.floor(x)
}

// 🎯 Calculate macros with randomization (5-10% variation)
const macroRatios = computed(() => {
  // Use meal name and calories as seed for consistent randomization
  const seed = (props.meal.name || '').length + props.meal.calories
  
  // Base ratios
  const baseProtein = 0.30 // 30%
  const baseCarbs = 0.40   // 40%
  const baseFats = 0.30    // 30%
  
  // Add random variation (5-10%)
  const proteinVariation = (seededRandom(seed) - 0.5) * 0.15        // ±7.5%
  const carbsVariation = (seededRandom(seed + 1) - 0.5) * 0.15      // ±7.5%
  const fatsVariation = (seededRandom(seed + 2) - 0.5) * 0.15       // ±7.5%
  
  let proteinRatio = baseProtein + proteinVariation
  let carbsRatio = baseCarbs + carbsVariation
  let fatsRatio = baseFats + fatsVariation
  
  // Ensure ratios stay within reasonable bounds (20-50% each)
  proteinRatio = Math.max(0.20, Math.min(0.50, proteinRatio))
  carbsRatio = Math.max(0.20, Math.min(0.50, carbsRatio))
  fatsRatio = Math.max(0.20, Math.min(0.50, fatsRatio))
  
  // Normalize to ensure they add up to 1
  const total = proteinRatio + carbsRatio + fatsRatio
  
  return {
    protein: proteinRatio / total,
    carbs: carbsRatio / total,
    fats: fatsRatio / total
  }
})

// 🎯 Calculate macros - use direct props if available, otherwise calculate from calories
const fats = computed(() => {
  if (props.fatsGrams !== null) return props.fatsGrams
  if (props.meal.fats !== undefined) return props.meal.fats
  return Math.round(props.meal.calories * macroRatios.value.fats / 9)
})

const carbs = computed(() => {
  if (props.carbsGrams !== null) return props.carbsGrams
  if (props.meal.carbs !== undefined) return props.meal.carbs
  return Math.round(props.meal.calories * macroRatios.value.carbs / 4)
})

const protein = computed(() => {
  if (props.proteinGrams !== null) return props.proteinGrams
  if (props.meal.protein !== undefined) return props.meal.protein
  return Math.round(props.meal.calories * macroRatios.value.protein / 4)
})

// 🎨 Theme-based colors
const themeColors = computed(() => {
  const themes = {
    default: {
      protein: { bg: '#EF4444', border: '#DC2626', hover: '#F87171' },
      carbs: { bg: '#3B82F6', border: '#2563EB', hover: '#60A5FA' },
      fats: { bg: '#F59E0B', border: '#D97706', hover: '#FBBF24' }
    },
    fancy: {
      protein: { bg: '#FF6B6B', border: '#E63946', hover: '#FF8787' },
      carbs: { bg: '#4ECDC4', border: '#26A69A', hover: '#80CBC4' },
      fats: { bg: '#FFE66D', border: '#FF8F00', hover: '#FFF176' }
    },
    neon: {
      protein: { bg: '#FF073A', border: '#FF073A', hover: '#FF4569' },
      carbs: { bg: '#00D4FF', border: '#00D4FF', hover: '#33DDFF' },
      fats: { bg: '#FFE135', border: '#FFE135', hover: '#FFEB3B' }
    }
  }
  return themes[props.theme] || themes.default
})

// 🎨 Enhanced Chart data
const chartData = computed(() => ({
  labels: ['Protein', 'Carbs', 'Fats'],
  datasets: [
    {
      data: [protein.value, carbs.value, fats.value],
      backgroundColor: [
        themeColors.value.protein.bg,
        themeColors.value.carbs.bg,
        themeColors.value.fats.bg
      ],
      borderColor: [
        themeColors.value.protein.border,
        themeColors.value.carbs.border,
        themeColors.value.fats.border
      ],
      borderWidth: props.size === 'fancy' || props.size === 'xl' ? 4 : 2,
      hoverBackgroundColor: [
        themeColors.value.protein.hover,
        themeColors.value.carbs.hover,
        themeColors.value.fats.hover
      ],
      hoverBorderWidth: props.size === 'fancy' || props.size === 'xl' ? 6 : 3,
      cutout: props.size === 'fancy' || props.size === 'xl' ? '60%' : '50%'
    }
  ]
}))

// 🎯 Enhanced Chart options
const chartOptions = computed(() => ({
  responsive: true,
  maintainAspectRatio: false,
  animation: {
    animateRotate: true,
    animateScale: true,
    duration: 1500,
    easing: 'easeOutBounce'
  },
  plugins: {
    legend: {
      display: props.showLegend,
      position: 'bottom',
      labels: {
        padding: props.size === 'fancy' || props.size === 'xl' ? 25 : 15,
        font: {
          size: props.size === 'fancy' || props.size === 'xl' ? 16 : 12,
          weight: 'bold'
        },
        usePointStyle: true,
        pointStyle: 'circle'
      }
    },
    title: {
      display: true,
      text: `${props.meal.name}`,
      font: {
        size: props.size === 'fancy' || props.size === 'xl' ? 20 : 14,
        weight: 'bold'
      },
      padding: {
        top: 10,
        bottom: 30
      }
    },
    tooltip: {
      backgroundColor: 'rgba(0, 0, 0, 0.8)',
      titleColor: '#fff',
      bodyColor: '#fff',
      cornerRadius: 10,
      displayColors: true,
      callbacks: {
        label: function(context) {
          const percentage = ((context.parsed / (protein.value + carbs.value + fats.value)) * 100).toFixed(1)
          return `${context.label}: ${context.parsed}g (${percentage}%)`
        }
      }
    }
  },
  elements: {
    arc: {
      borderWidth: props.size === 'fancy' || props.size === 'xl' ? 4 : 2,
      hoverBorderWidth: props.size === 'fancy' || props.size === 'xl' ? 6 : 3
    }
  }
}))

// 🎨 Size classes
const sizeClasses = computed(() => {
  const sizes = {
    small: 'h-32 w-32',
    medium: 'h-48 w-48',
    large: 'h-64 w-64',
    xl: 'h-80 w-80',
    fancy: 'h-96 w-96'
  }
  return sizes[props.size] || sizes.medium
})

// 🎨 Container classes
const containerClasses = computed(() => {
  const isLarge = props.size === 'fancy' || props.size === 'xl'
  return {
    'shadow-2xl': isLarge,
    'shadow-lg': !isLarge,
    'bg-gradient-to-br from-white to-gray-50': isLarge,
    'bg-white': !isLarge,
    'border-2 border-gray-100': isLarge,
    'transform hover:scale-105 transition-all duration-300': isLarge
  }
})

// 🎯 Calculate total calories from macros if using direct macro props
const totalCalories = computed(() => {
  if (props.proteinGrams !== null || props.meal.protein !== undefined) {
    return (protein.value * 4) + (carbs.value * 4) + (fats.value * 9)
  }
  return props.meal.calories
})

// 🎯 Calculate percentages with actual values
const totalMacros = computed(() => protein.value + carbs.value + fats.value)
const proteinPercentage = computed(() => Math.round((protein.value / totalMacros.value) * 100))
const carbsPercentage = computed(() => Math.round((carbs.value / totalMacros.value) * 100))
const fatsPercentage = computed(() => Math.round((fats.value / totalMacros.value) * 100))

// 🎲 Display the actual macro ratios for debugging (optional)
const macroRatioPercentages = computed(() => ({
  protein: Math.round(macroRatios.value.protein * 100),
  carbs: Math.round(macroRatios.value.carbs * 100),
  fats: Math.round(macroRatios.value.fats * 100)
}))

// 🍽️ Save meal function
function saveMeal() {
  const mealWithMacros = {
    id: Date.now(), // Generate unique ID
    name: props.meal.name,
    calories: totalCalories.value,
    macros: {
      protein: {
        grams: protein.value,
        calories: protein.value * 4,
        percentage: proteinPercentage.value
      },
      carbs: {
        grams: carbs.value,
        calories: carbs.value * 4,
        percentage: carbsPercentage.value
      },
      fats: {
        grams: fats.value,
        calories: fats.value * 9,
        percentage: fatsPercentage.value
      }
    },
    savedAt: new Date().toISOString()
  }
  
  emit('save-meal', mealWithMacros)
}
</script>

<template>
  <div class="flex flex-col items-center space-y-6">
    <!-- 🎯 Enhanced Chart Container -->
    <div 
      :class="[
        'relative p-6 rounded-2xl',
        containerClasses,
        { 'backdrop-blur-sm': size === 'fancy' }
      ]"
    >
      <!-- Center Text for large charts -->
      <div 
        v-if="size === 'fancy' || size === 'xl'"
        class="absolute inset-0 flex items-center justify-center pointer-events-none"
      >
        <div class="text-center">
          <div class="text-3xl font-bold text-gray-800">{{ totalCalories }}</div>
          <div class="text-sm text-gray-500 uppercase tracking-wide">Calories</div>
        </div>
      </div>
      
      <div :class="sizeClasses">
        <Doughnut 
          :data="chartData" 
          :options="chartOptions" 
        />
      </div>
    </div>
    
    <!-- 🎯 Enhanced Macro Stats -->
    <div 
      :class="[
        'bg-gradient-to-br from-gray-50 to-white p-6 rounded-2xl w-full shadow-lg border border-gray-100',
        size === 'fancy' || size === 'xl' ? 'max-w-lg' : 'max-w-xs'
      ]"
    >
      <h4 class="font-bold text-gray-800 mb-4 text-center text-lg">
        Nutritional Breakdown
      </h4>
      
      <div class="space-y-4">
        <!-- Calories -->
        <div class="text-center p-3 bg-white rounded-xl shadow-sm">
          <div class="text-2xl font-bold text-gray-800">{{ totalCalories }}</div>
          <div class="text-sm text-gray-500 uppercase tracking-wide">Total Calories</div>
        </div>
        
        <!-- Enhanced Macro Display with Randomized Percentages -->
        <div class="space-y-3">
          <!-- Protein -->
          <div class="flex items-center justify-between p-3 bg-white rounded-xl shadow-sm">
            <div class="flex items-center space-x-3">
              <div class="w-4 h-4 bg-red-500 rounded-full shadow-sm"></div>
              <div>
                <div class="text-sm font-medium text-gray-700">🥩 Protein</div>
                <div class="text-xs text-gray-500">{{ proteinPercentage }}% of macros</div>
              </div>
            </div>
            <div class="text-right">
              <div class="font-bold text-gray-800">{{ protein }}g</div>
              <div class="text-xs text-gray-500">{{ protein * 4 }} cal</div>
            </div>
          </div>
          
          <!-- Carbs -->
          <div class="flex items-center justify-between p-3 bg-white rounded-xl shadow-sm">
            <div class="flex items-center space-x-3">
              <div class="w-4 h-4 bg-blue-500 rounded-full shadow-sm"></div>
              <div>
                <div class="text-sm font-medium text-gray-700">🍞 Carbs</div>
                <div class="text-xs text-gray-500">{{ carbsPercentage }}% of macros</div>
              </div>
            </div>
            <div class="text-right">
              <div class="font-bold text-gray-800">{{ carbs }}g</div>
              <div class="text-xs text-gray-500">{{ carbs * 4 }} cal</div>
            </div>
          </div>
          
          <!-- Fats -->
          <div class="flex items-center justify-between p-3 bg-white rounded-xl shadow-sm">
            <div class="flex items-center space-x-3">
              <div class="w-4 h-4 bg-orange-500 rounded-full shadow-sm"></div>
              <div>
                <div class="text-sm font-medium text-gray-700">🥑 Fats</div>
                <div class="text-xs text-gray-500">{{ fatsPercentage }}% of macros</div>
              </div>
            </div>
            <div class="text-right">
              <div class="font-bold text-gray-800">{{ fats }}g</div>
              <div class="text-xs text-gray-500">{{ fats * 9 }} cal</div>
            </div>
          </div>
        </div>
        
        <!-- 🍽️ Save Meal Button -->
        <div v-if="showSaveButton" class="mt-6 pt-4 border-t border-gray-200">
          <button
            @click="saveMeal"
            class="w-full bg-gradient-to-r from-green-500 to-green-600 hover:from-green-600 hover:to-green-700 text-white font-semibold py-3 px-6 rounded-xl shadow-lg hover:shadow-xl transform hover:scale-105 transition-all duration-200 flex items-center justify-center space-x-2"
          >
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6"/>
            </svg>
            <span>Save to My Meal List</span>
          </button>
        </div>
      </div>
    </div>
  </div>
</template> 