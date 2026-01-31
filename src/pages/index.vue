<script setup lang="ts">
import { ref, computed } from 'vue';
import { storeToRefs } from 'pinia';
import { useBaseStore } from '../stores/useBaseStore';

// Init Store
const baseStore = useBaseStore();
const { chains, config, testnets } = storeToRefs(baseStore) as any;

const keyword = ref('');

// --- UPDATE: DAFTAR MANUAL DENGAN SYMBOL MASING-MASING ---
const manualChains = [
  { id: 'republic-testnet',   logo: '/logos/republic.jpg', symbol: 'RAI' }, 
  { id: 'safrochain-testnet', logo: '/logos/safro.jpg',    symbol: 'SAF' }, 
  { id: 'lumen',              logo: '/logos/lumen.png',    symbol: 'LMN' },
  { id: 'osmosis',            logo: '/logos/osmo.jpg',  symbol: 'OSMO' }
]; 

const showNames = computed(() => {
  // 1. Ambil data otomatis dari store
  let list = chains?.value || config?.value?.chains || testnets?.value || [];
  if (!Array.isArray(list)) list = [];

  // 2. Gabungkan/Prioritaskan data manual agar logo dan symbol sesuai
  if (list.length === 0 || manualChains.length > 0) {
      const manualList = manualChains.map(item => ({
          chain_name: item.id,
          name: item.id, 
          logo: item.logo,
          symbol: item.symbol // <-- SEKARANG SYMBOL TERDAFTAR
      }));
      
      list = manualList;
  }

  // 3. Filter Search
  if(keyword.value) {
      return list.filter((chain: any) => {
          const name = chain.chain_name || chain.name || '';
          return name.toLowerCase().includes(keyword.value.toLowerCase());
      });
  }
  
  return list;
});

const handleImageError = (e: Event) => {
  (e.target as HTMLImageElement).src = '/favicon.ico';
};
</script>

<template>
  <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-12">
    
    <div class="text-center mb-12">
      <h1 class="text-4xl font-extrabold text-gray-900 dark:text-white sm:text-5xl sm:tracking-tight lg:text-6xl mb-4">
        StringLabs <span class="text-blue-600">Explorer</span>
      </h1>
      <p class="max-w-xl mx-auto text-xl text-gray-500 dark:text-gray-400">
        Search and explore cosmos blockchain networks.
      </p>
    </div>

    <div class="max-w-xl mx-auto mb-16">
      <div class="relative rounded-md shadow-sm">
        <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
          <i class='bx bx-search text-gray-400 text-xl'></i>
        </div>
        <input 
          v-model="keyword" 
          type="text" 
          class="focus:ring-blue-500 focus:border-blue-500 block w-full pl-10 sm:text-sm border-gray-300 dark:border-gray-700 dark:bg-gray-800 dark:text-white rounded-md py-4 shadow-sm" 
          placeholder="Search chains..." 
        />
      </div>
    </div>

    <div class="grid grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-3">
      <div 
        v-for="chain in showNames" 
        :key="chain.chain_name || chain.name" 
        class="bg-white dark:bg-gray-800 overflow-hidden shadow rounded-lg hover:shadow-lg transition-shadow duration-200 border border-transparent hover:border-blue-500/30"
      >
        <a :href="`/${chain.chain_name || chain.name}`" class="block px-6 py-6">
          <div class="flex items-center">
            <div class="flex-shrink-0 h-12 w-12">
              <img 
                class="h-12 w-12 rounded-full bg-gray-100 dark:bg-gray-700 p-1 object-contain" 
                :src="chain.logo" 
                @error="handleImageError"
                alt="" 
              />
            </div>
            <div class="ml-4">
              <h3 class="text-lg leading-6 font-bold text-gray-900 dark:text-white capitalize">
                {{ (chain.chain_name || chain.name).replace(/-/g, ' ') }}
              </h3>
              <p class="mt-1 text-sm text-blue-400 font-bold font-mono">
                {{ chain.symbol || 'TOKEN' }}
              </p>
            </div>
          </div>
        </a>
      </div>
    </div>
  </div>
</template>