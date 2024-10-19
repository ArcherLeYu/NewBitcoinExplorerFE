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
    <div v-if="blockchainInfo" class="block-info">
      <!-- Existing Blockchain Info Display -->
      <h2>Blockchain Info</h2>
      <p><strong>Block Height:</strong> {{ blockchainInfo.blocks }}</p>
      <p><strong>Chain:</strong> {{ blockchainInfo.chain }}</p>
      <p><strong>Chain Work:</strong> {{ blockchainInfo.chainwork }}</p>
      <p><strong>Difficulty:</strong> {{ blockchainInfo.difficulty }}</p>
      <p><strong>Headers:</strong> {{ blockchainInfo.headers }}</p>
<!--      <p><strong>Initial Block Download:</strong> {{ blockchainInfo.initialBlockDownload ? 'Yes' : 'No' }}</p>-->
<!--      <p><strong>Median Time:</strong> {{ blockchainInfo.medianTime }}</p>-->
<!--      <p><strong>Prune Target Size:</strong> {{ blockchainInfo.pruneTargetSize }}</p>-->
<!--      <p><strong>Pruned:</strong> {{ blockchainInfo.pruned ? 'Yes' : 'No' }}</p>-->
<!--      <p><strong>Prune Height:</strong> {{ blockchainInfo.pruneHeight }}</p>-->
<!--      <p><strong>Size on Disk:</strong> {{ blockchainInfo.sizeOnDisk }}</p>-->
      <p><strong>Time:</strong> {{ blockchainInfo.time }}</p>
<!--      <p><strong>Verification Progress:</strong> {{ blockchainInfo.verificationProgress }}</p>-->
<!--      <p><strong>Warnings:</strong> {{ blockchainInfo.warnings }}</p>-->
<!--      <p><strong>Automatic Pruning:</strong> {{ blockchainInfo.automaticPruning ? 'Yes' : 'No' }}</p>-->
      <p><strong>Best Block Hash:</strong> {{ blockchainInfo.bestBlockHash }}</p>
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

// 定义区块链信息的类型
interface BlockchainInfo {
  blocks: number;
  chain: string;
  chainwork: string;
  difficulty: number;
  headers: number;
  initialBlockDownload: boolean;
  medianTime: number;
  pruneTargetSize: number;
  pruned: boolean;
  pruneHeight: number;
  sizeOnDisk: number;
  time: number;
  verificationProgress: number;
  warnings: string;
  automaticPruning: boolean;
  bestBlockHash: string;
}

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
    const blockchainInfo = ref<BlockchainInfo | null>(null)
    const error = ref<string | null>(null)
    const bitcoinPrice = ref<number | null>(null)
    const bitcoinVolume = ref<number | null>(null)
    const bitcoinPriceList = ref<number[]>([])
    const latestBlocks = ref<BlockSummary[]>([])

    // 在组件挂载时从后端获取数据
    onMounted(async () => {
      // Existing blockchain info fetch
      try {
        const blockchainResponse = await axios.get('http://127.0.0.1:8081/blockchain-info')
        blockchainInfo.value = convertToCamelCase(blockchainResponse.data)
        console.log('Fetched chaininfo:', blockchainInfo.value)
      } catch (err) {
        console.error('Error fetching blockchain info:', err)
        error.value = 'Failed to fetch blockchain information.'
      }

      // Fetching latest price
      try {
        const priceResponse = await axios.get('http://127.0.0.1:8081/latest-price')
        bitcoinPrice.value = priceResponse.data
        console.log('Fetched Bitcoin Price:', bitcoinPrice.value)
      } catch (err) {
        console.error('Error fetching bitcoin price:', err)
        error.value = 'Failed to fetch bitcoin price.'
      }

      // Fetching latest volume
      try {
        const volumeResponse = await axios.get('http://127.0.0.1:8081/latest-volume')
        bitcoinVolume.value = volumeResponse.data
        console.log('Fetched Bitcoin Volume:', bitcoinVolume.value)
      } catch (err) {
        console.error('Error fetching bitcoin volume:', err)
        error.value = 'Failed to fetch bitcoin volume.'
      }

      // Fetch the latest block summaries
      try {
        const blocksResponse = await axios.get('http://127.0.0.1:8081/blocks-summary')
        latestBlocks.value = blocksResponse.data.slice(0, 5) // Use only the latest 5 blocks
        console.log('Fetched latest 5 block summaries:', latestBlocks.value)
      } catch (err) {
        console.error('Error fetching block summaries:', err)
        error.value = 'Failed to fetch block summaries.'
      }

      // fetching latest 10
      try {
        const response = await axios.get('http://127.0.0.1:8081/latest-10-prices')
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
      bitcoinPriceList,
      latestBlocks,
      blockchainInfo,
      bitcoinPrice,
      bitcoinVolume,
      error
    }
  }
})

// 处理后端返回的数据并进行转换
function convertToCamelCase (data: any): BlockchainInfo {
  return {
    blocks: data.blocks,
    chain: data.chain,
    chainwork: data.chainwork,
    difficulty: data.difficulty,
    headers: data.headers,
    initialBlockDownload: data.initialblockdownload,
    medianTime: data.mediantime,
    pruneTargetSize: data.prune_target_size,
    pruned: data.pruned,
    pruneHeight: data.pruneheight,
    sizeOnDisk: data.size_on_disk,
    time: data.time,
    verificationProgress: data.verificationprogress,
    warnings: data.warnings,
    automaticPruning: data.automatic_pruning,
    bestBlockHash: data.bestblockhash
  }
}
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
