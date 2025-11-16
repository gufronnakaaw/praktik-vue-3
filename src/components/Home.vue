<template>
  <div class="app-container">
    <button class="refresh-btn" @click="refetchCoin">Refresh</button>

    <p v-if="loading">loading...</p>
    <p v-else-if="errorMessage">{{ errorMessage }}</p>
    <div v-else class="list">
      <div class="item" v-for="coin in coins" :key="coin.rank">
        <div class="left">
          <div class="rank">Rank {{ coin.rank }}</div>
          <div class="name">{{ coin.name }}</div>
          <div class="symbol">{{ coin.symbol }}</div>
        </div>
        <div class="right">
          <div class="usd">USD</div>
          <div class="price">{{ coin.price_usd }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import type { CryptoResponse } from '@/types/CryptoResponse'
import { onMounted, ref } from 'vue'

const loading = ref<boolean>(true)

const errorMessage = ref<string>('')
const coins = ref<{ rank: number, name: string, symbol: string, price_usd: string}[] | []>([])

function refetchCoin(){
  loading.value = true;
  fetchCoin()
}

onMounted(async () => {
  fetchCoin()
})

async function fetchCoin(){
  try {
    const response = await fetch('https://api.coinlore.net/api/tickers/')

    if(!response.ok){
      throw new Error('Sesuatu telah terjadi')
    }

    const data: CryptoResponse = await response.json()

    coins.value = data.data.map(item => {
      return {
        rank: item.rank,
        name: item.name,
        symbol: item.symbol,
        price_usd: item.price_usd
      }
    })

  } catch (error: any) {
    errorMessage.value = error.message
  } finally {
    loading.value = false;
  }
}

</script>

<style scoped>
p {
  text-align: center;
}

.app-container {
  width: 100%;
  max-width: 420px;
  margin: 0 auto;
  padding: 24px;
  font-family: Inter, sans-serif;
  background: #fafafa;
}

.refresh-btn {
  width: 100%;
  background: #ef9e3a;
  border: none;
  color: white;
  padding: 14px;
  border-radius: 10px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  margin-bottom: 24px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.08);
  transition: 0.2s ease;
}
.refresh-btn:hover {
  opacity: 0.9;
}

.list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.item {
  background: #ffffff;
  border-radius: 14px;
  padding: 18px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0 2px 6px rgba(0,0,0,0.06);
  border: 1px solid #e5e5e5;
}

.left .rank {
  font-size: 13px;
  color: #6b7280;
  font-weight: 500;
}

.left .name {
  font-size: 15px;
  font-weight: 600;
  margin-top: 4px;
  color: #111827;
}

.left .symbol {
  font-size: 22px;
  font-weight: 700;
  margin-top: 2px;
  color: #1f2937;
}

.right {
  text-align: right;
}

.usd {
  font-size: 12px;
  color: #9ca3af;
  font-weight: 500;
}

.price {
  font-size: 20px;
  margin-top: 2px;
  font-weight: 700;
  color: #111827;
}
</style>
