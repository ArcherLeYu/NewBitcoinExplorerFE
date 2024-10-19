<template>
  <div class="chart-container">
    <h3>Bitcoin Price Over Time</h3>
    <Line :data="chartData" :options="options" />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch, PropType } from 'vue'
import { Line } from 'vue-chartjs'
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  LineElement,
  CategoryScale,
  LinearScale,
  PointElement
} from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, LineElement, CategoryScale, LinearScale, PointElement)

export default defineComponent({
  name: 'PriceChart',
  components: {
    Line
  },
  props: {
    priceData: {
      type: Array as PropType<number[]>,
      required: true
    }
  },
  setup (props) {
    const chartData = ref({
      labels: Array(10).fill(''), // Initial placeholder labels, will update when data changes
      datasets: [
        {
          label: 'Bitcoin Price (USD)',
          backgroundColor: 'rgba(54, 162, 235, 0.2)',
          borderColor: 'rgba(54, 162, 235, 1)',
          data: Array(10).fill(0), // Initial placeholder data
          fill: true
        }
      ]
    })

    // Add the watch function here
    watch(
      () => props.priceData,
      (newPrices) => {
        console.log('Updating chart with new prices:', newPrices)
        chartData.value = {
          labels: newPrices.map((_, index) => `Point ${index + 1}`), // Dynamic labels
          datasets: [
            {
              label: 'Bitcoin Price (USD)',
              backgroundColor: 'rgba(54, 162, 235, 0.2)',
              borderColor: 'rgba(54, 162, 235, 1)',
              data: newPrices,
              fill: true
            }
          ]
        }
      },
      { immediate: true }
    )

    const options = ref({
      responsive: true,
      maintainAspectRatio: true,
      scales: {
        y: {
          min: 68200, // Set the minimum value of the Y-axis
          max: 68600, // Set the maximum value of the Y-axis
          title: {
            display: true,
            text: 'Price in USD'
          }
        },
        x: {
          title: {
            display: true,
            text: 'Date'
          }
        }
      }
    })

    return {
      chartData,
      options
    }
  }
})
</script>

<style scoped>
.chart-container {
  width: 100%;
  max-width: 800px;
  height: 400px;
  margin: 0 auto;
}

h3 {
  text-align: center;
  margin-bottom: 20px;
}
</style>
