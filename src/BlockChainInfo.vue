<template>
  <div id="app">
    <h1>Bitcoin Explorer</h1>
    <!-- Display the last 5 blocks with fixed data in squares -->
    <div class="block-container">
      <div v-for="block in latestBlocks" :key="block.height" class="block-square">
        <p><strong>Height:</strong> {{ block.height }}</p>
        <p class="block-hash">
          <strong>Hash:</strong> {{ block.hash.substring(0, 10) }}... <!-- Show only the first 10 characters of the hash -->
        </p>      </div>
    </div>
    <div v-if="latestOneBlock" class="block-info">
      <h2>On Chain</h2>
      <p><strong>Block Height:</strong> {{ latestOneBlock.height }}</p>
      <p><strong>Block Hash:</strong> {{ latestOneBlock.hash }}</p>
    </div>
    <div v-if="bitcoinPrice || bitcoinVolume" class="offchain-info">
      <h2>Market Data</h2>
      <price-chart :price-data="bitcoinPriceList" />
      <p v-if="bitcoinPrice"><strong>Current Bitcoin Price:</strong> ${{ bitcoinPrice }}</p>
      <p v-if="bitcoinVolume"><strong>Current Bitcoin Volume:</strong> {{ bitcoinVolume }}</p>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue'
import axios from 'axios' // 引入 axios
import PriceChart from './PriceChart.vue'

// Define the type for a block summary
interface BlockSummary {
  height: number;
  hash: string;
}

export default defineComponent({
  name: 'BlockChainInfo',
  components: {
    PriceChart
  },
  setup () {
    const error = ref<string | null>(null)
    const bitcoinPrice = ref<number | null>(null)
    const bitcoinVolume = ref<number | null>(null)
    const bitcoinPriceList = ref<number[]>([])
    const latestBlocks = ref<BlockSummary[]>([])
    const latestOneBlock = ref<BlockSummary | null>(null) // Use a single block object

    // 在组件挂载时从后端获取数据
    onMounted(async () => {
      // Fetching latest price
      try {
        const priceResponse = await axios.get('http://ec2-18-222-131-51.us-east-2.compute.amazonaws.com:8081/latest-price')
        bitcoinPrice.value = priceResponse.data
        console.log('Fetched Bitcoin Price:', bitcoinPrice.value)
      } catch (err) {
        console.error('Error fetching bitcoin price:', err)
        error.value = 'Failed to fetch bitcoin price.'
      }

      // Fetching latest volume
      try {
        const volumeResponse = await axios.get('http://ec2-18-222-131-51.us-east-2.compute.amazonaws.com:8081/latest-volume')
        bitcoinVolume.value = volumeResponse.data
        console.log('Fetched Bitcoin Volume:', bitcoinVolume.value)
      } catch (err) {
        console.error('Error fetching bitcoin volume:', err)
        error.value = 'Failed to fetch bitcoin volume.'
      }

      // Fetch the latest block summaries
      try {
        const blocksResponse = await axios.get('http://ec2-18-222-131-51.us-east-2.compute.amazonaws.com:8081/blocks-summary')
        latestBlocks.value = blocksResponse.data.slice(0, 5) // Store the latest 5 blocks
        latestOneBlock.value = blocksResponse.data[0] // Store the most recent block as a single object
        console.log('Fetched latest 5 block summaries:', latestBlocks.value)
        console.log('Fetched latest one block:', latestOneBlock.value)
      } catch (err) {
        console.error('Error fetching block summaries:', err)
        error.value = 'Failed to fetch block summaries.'
      }

      // fetching latest 10
      try {
        const response = await axios.get('http://ec2-18-222-131-51.us-east-2.compute.amazonaws.com:8081/latest-10-prices')
        bitcoinPriceList.value = response.data.prices
        console.log('Fetched 10 latest Bitcoin Prices:', bitcoinPriceList.value)
      } catch (err) {
        console.error('Error fetching 10 latest prices:', err)
      }
    })

    // Additional logs to verify data before rendering
    console.log('Price before rendering:', bitcoinPrice.value) // Add this
    console.log('Volume before rendering:', bitcoinVolume.value)// Add this
    return {
      latestOneBlock,
      bitcoinPriceList,
      latestBlocks,
      bitcoinPrice,
      bitcoinVolume,
      error
    }
  }
})

</script>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.block-container {
  display: flex;
  justify-content: center;
  gap: 10px; /* Space between blocks */
  margin-bottom: 20px;
}

.block-square {
  border: 2px solid #ddd;
  padding: 10px;
  border-radius: 8px;
  background-color: #f5f5f5;
  width: 150px;
  height: 100px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  font-size: 14px;
  color: #333;
  overflow: hidden;
  text-overflow: ellipsis;
  word-wrap: break-word;
}

.block-info {
  border: 1px solid #e0e0e0;
  padding: 20px;
  border-radius: 8px;
  width: 60%;
  margin: auto;
  background-color: #f9f9f9;
}

.offchain-info {
  border: 1px dashed #4CAF50; /* 使用虚线边框和不同的颜色 */
  padding: 20px;
  border-radius: 8px;
  width: 60%;
  margin: 30px auto; /* 添加更多的上下边距以区分 */
  background-color: #e8f5e9; /* 浅绿色背景提高区分度 */
}

.offchain-info p {
  font-size: 16px;
  line-height: 1.8; /* 调整行高 */
  color: #388E3C; /* 文本使用深绿色 */
}

.offchain-info h2 {
  color: #2E7D32; /* 标题使用更深的绿色 */
}

.error-message {
  color: red;
  font-weight: bold;
  margin-top: 20px;
}
</style>
