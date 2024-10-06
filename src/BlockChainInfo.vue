<template>
  <div id="app">
    <h1>Bitcoin Explorer</h1>
    <div v-if="blockchainInfo" class="block-info">
      <h2>Blockchain Info</h2>
      <p><strong>Block Height:</strong> {{ blockchainInfo.blocks }}</p>
      <p><strong>Chain:</strong> {{ blockchainInfo.chain }}</p>
      <p><strong>Chain Work:</strong> {{ blockchainInfo.chainwork }}</p>
      <p><strong>Difficulty:</strong> {{ blockchainInfo.difficulty }}</p>
      <p><strong>Headers:</strong> {{ blockchainInfo.headers }}</p>
      <p><strong>Initial Block Download:</strong> {{ blockchainInfo.initialBlockDownload ? 'Yes' : 'No' }}</p>
      <p><strong>Median Time:</strong> {{ blockchainInfo.medianTime }}</p>
      <p><strong>Prune Target Size:</strong> {{ blockchainInfo.pruneTargetSize }}</p>
      <p><strong>Pruned:</strong> {{ blockchainInfo.pruned ? 'Yes' : 'No' }}</p>
      <p><strong>Prune Height:</strong> {{ blockchainInfo.pruneHeight }}</p>
      <p><strong>Size on Disk:</strong> {{ blockchainInfo.sizeOnDisk }}</p>
      <p><strong>Time:</strong> {{ blockchainInfo.time }}</p>
      <p><strong>Verification Progress:</strong> {{ blockchainInfo.verificationProgress }}</p>
      <p><strong>Warnings:</strong> {{ blockchainInfo.warnings }}</p>
      <p><strong>Automatic Pruning:</strong> {{ blockchainInfo.automaticPruning ? 'Yes' : 'No' }}</p>
      <p><strong>Best Block Hash:</strong> {{ blockchainInfo.bestBlockHash }}</p>
    </div>
    <div v-if="error" class="error-message">
      <p>Error: {{ error }}</p>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue'
import axios from 'axios' // 引入 axios

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

export default defineComponent({
  name: 'BlockChainInfo',
  setup () {
    const blockchainInfo = ref<BlockchainInfo | null>(null)
    const error = ref<string | null>(null)

    // 在组件挂载时从后端获取数据
    onMounted(async () => {
      console.log('Component mounted. Preparing to fetch blockchain information...') // 打印日志，表示组件挂载成功

      try {
        console.log('Sending request to backend API at http://127.0.0.1:8081/blockchain-info') // 打印日志，表示即将发送请求
        // 这里替换为你的后端 API 地址
        const response = await axios.get('http://127.0.0.1:8081/blockchain-info')
        console.log('Received response from backend:', response) // 打印完整的响应，帮助排查数据结构问题
        // 将下划线命名的数据转换为驼峰式命名
        blockchainInfo.value = convertToCamelCase(response.data)
        console.log('Blockchain information successfully set:', blockchainInfo.value) // 打印日志，表示数据设置成功
      } catch (err) {
        console.error('Error occurred while fetching blockchain information:', err) // 打印详细错误
        error.value = 'Failed to fetch blockchain information.'
      }
    })

    return {
      blockchainInfo,
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

.block-info {
  border: 1px solid #e0e0e0;
  padding: 20px;
  border-radius: 8px;
  width: 60%;
  margin: auto;
  background-color: #f9f9f9;
}

.block-info p {
  font-size: 16px;
  line-height: 1.6;
}

.block-info h2 {
  margin-bottom: 20px;
}

.error-message {
  color: red;
  font-weight: bold;
  margin-top: 20px;
}
</style>
