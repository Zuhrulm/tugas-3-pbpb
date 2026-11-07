<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar color="primary">
        <ion-title>Cryptocurrency Tickers</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <!-- Indikator Loading -->
      <div v-if="loading" class="ion-text-center ion-padding">
        <ion-spinner name="crescent"></ion-spinner>
        <p>Memuat data cryptocurrency...</p>
      </div>

      <!-- Pesan Error -->
      <div v-else-if="error" class="ion-text-center ion-padding text-danger">
        <p>{{ error }}</p>
      </div>

      <!-- Tampilan Daftar Crypto -->
      <ion-list v-else>
        <ion-item v-for="crypto in cryptoList" :key="crypto.id">
          <ion-label>
            <div class="crypto-header">
              <h2><strong>#{{ crypto.rank }}</strong> - {{ crypto.name }}</h2>
              <span class="price">${{ parseFloat(crypto.price_usd).toLocaleString() }}</span>
            </div>
            <p>Simbol: <strong>{{ crypto.symbol }}</strong></p>
          </ion-label>
        </ion-item>
      </ion-list>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { 
  IonPage, IonHeader, IonToolbar, IonTitle, 
  IonContent, IonList, IonItem, IonLabel, IonSpinner 
} from '@ionic/vue';

interface CryptoItem {
  id: string;
  symbol: string;
  name: string;
  rank: number;
  price_usd: string;
}

const cryptoList = ref<CryptoItem[]>([]);
const loading = ref<boolean>(true);
const error = ref<string | null>(null);

const fetchCryptos = async () => {
  try {
    loading.value = true;
    const response = await fetch('https://api.coinlore.net/api/tickers/');
    if (!response.ok) throw new Error('Gagal mengambil data.');
    const data = await response.json();
    cryptoList.value = data.data;
  } catch (err: any) {
    error.value = err.message || 'Terjadi kesalahan.';
  } finally {
    loading.value = false;
  }
};

onMounted(() => {
  fetchCryptos();
});
</script>

<style scoped>
.crypto-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.price {
  font-weight: 600;
  color: var(--ion-color-success, #2dd36f);
}
</style>