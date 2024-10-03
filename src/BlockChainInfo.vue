<template>
  <div id="app">
    <h1>Bitcoin Explorer</h1>
    <div class="block-info">
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
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

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
  name: 'App',
  data () {
    return {
      blockchainInfo: {
        blocks: 754312,
        chain: 'main',
        chainwork: '0000000000000000000b4d0f5efb7eaa49d5f0e6143f45c5e0bc32fdd8c1a112',
        difficulty: 15348939240.0123,
        headers: 754312,
        initialBlockDownload: false,
        medianTime: 1629318000,
        pruneTargetSize: 0,
        pruned: false,
        pruneHeight: 0,
        sizeOnDisk: 3402789632,
        time: 1629321000,
        verificationProgress: 0.999876,
        warnings: 'No warnings',
        automaticPruning: false,
        bestBlockHash: '0000000000000000000b4d0f5efb7eaa49d5f0e6143f45c5e0bc32fdd8c1a112'
      } as BlockchainInfo
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
</style>
