<script setup>
import { ref, onMounted } from 'vue'
import { 
    Chart as ChartJS,
    Title,
    Tooltip,
    Legend,
    BarElement,
    CategoryScale,
    LinearScale,
    ArcElement
} from 'chart.js'
import { Bar, Doughnut } from 'vue-chartjs'

ChartJS.register(
    Title, 
    Tooltip,
    Legend, 
    BarElement,
    CategoryScale,
    LinearScale,
    ArcElement
)

const chartData = ref({
    labels: ['Protein', 'Carbs', 'Fats'],
    datasets: [
        {
            labels: 'M'
        }
    ]
})
</script>
