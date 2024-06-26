<template>
  <div>
    <div class="button-container">
      <button class="Customise-button" @click="showModal = true">Customise</button>
      <Modal v-if="showModal" @close="showModal = false" :options="availableOptions" @save="saveColumns"></Modal>
    </div>
    <table class="table-auto w-full mt-8">
      <thead>
        <tr>
          <th v-for="column in visibleColumns" :key="column.key" @click="sort(column.key)">
            {{ column.label }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(coin, index) in sortedCoins" :key="index" class="border-t-2 border-gray-300">
          <td v-for="column in visibleColumns" :key="column.key" class="px-4 py-4 text-black">
            <template v-if="column.key !== 'image'">
              {{ coin[column.key] }}
            </template>
            <template v-else>
              <img :src="coin[column.key]" class="coin-image" alt="Coin Image">
            </template>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import { ref, reactive, onMounted, computed } from 'vue';
import axios from 'axios';
import Modal from './Modal.vue';

export default {
  components: {
    Modal,
  },
  setup() {
    const showModal = ref(false);
    const availableOptions = [
      { key: 'fully_diluted_valuation', label: 'Fully Diluted Valuation' },
      { key: 'total_volume', label: 'Total Volume' },
      { key: 'high_24h', label: 'High 24h' },
      { key: 'price_change_24h', label: 'Price Change 24h' },
      { key: 'market_cap_change_24h', label: 'Market Cap Change 24h' },
      { key: 'market_cap_change_percentage_24h', label: 'Market Cap Change Percentage 24h' },
      { key: 'circulating_supply', label: 'Circulating Supply' },
      { key: 'total_supply', label: 'Total Supply' },
      { key: 'max_supply', label: 'Max Supply' },
      { key: 'ath', label: 'Ath' },
      { key: 'ath_change_percentage', label: 'Ath Change Percentage' },
      { key: 'ath_date', label: 'Ath Date' },
      { key: 'atl', label: 'Atl' },
      { key: 'atl_change_percentage', label: 'Atl Change Percentage' },
      { key: 'atl_date', label: 'Atl Date' },
      { key: 'roi', label: 'ROI' },
      { key: 'last_updated', label: 'Last Updated' },
    ];
    const defaultColumns = [
      { key: 'market_cap_rank', label: '#' },
      { key: 'image', label: 'Image' },
      { key: 'name', label: 'Name' },
      { key: 'current_price', label: 'Current Price' },
      { key: 'market_cap', label: 'Market Cap' },
      { key: 'price_change_percentage_24h', label: 'Price Change Percentage 24h' },
    ];
    const visibleColumns = reactive([]);
    const coins = ref([]);
    const sortKey = ref('');
    const sortOrders = reactive({});

    const fetchData = () => {
      const url = 'https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false';
      axios.get(url)
        .then(response => {
          coins.value = response.data.map(coin => ({
            market_cap_rank: coin.market_cap_rank,
            image: coin.image,
            name: coin.name,
            current_price: coin.current_price,
            market_cap: coin.market_cap,
            price_change_percentage_24h: coin.price_change_percentage_24h,
            fully_diluted_valuation: coin.fully_diluted_valuation,
            total_volume: coin.total_volume,
            high_24h: coin.high_24h,
            price_change_24h: coin.price_change_24h,
            market_cap_change_24h: coin.market_cap_change_24h,
            market_cap_change_percentage_24h: coin.market_cap_change_percentage_24h,
            circulating_supply: coin.circulating_supply,
            total_supply: coin.total_supply,
            max_supply: coin.max_supply,
            ath: coin.ath,
            ath_change_percentage: coin.ath_change_percentage,
            ath_date: coin.ath_date,
            atl: coin.atl,
            atl_change_percentage: coin.atl_change_percentage,
            atl_date: new Date(coin.atl_date).toLocaleDateString('en-US', { day: '2-digit', month: 'short', year: 'numeric' }),
            roi: coin.roi ? coin.roi : 'N/A',
            last_updated: new Date(coin.last_updated).toLocaleDateString('en-US', { day: '2-digit', month: 'short', year: 'numeric' }),
          }));
          setDefaultColumns();
        })
        .catch(error => {
          console.error('Error fetching data:', error);
        });
    };

    const setDefaultColumns = () => {
      visibleColumns.splice(0, visibleColumns.length, ...defaultColumns);
    };

    const sort = (key) => {
      sortKey.value = key;
      sortOrders[key] = sortOrders[key] === 'desc' ? 'asc' : 'desc';
    };

    const saveColumns = (newColumns) => {
      visibleColumns.splice(0, visibleColumns.length, ...defaultColumns, ...newColumns);
    };

    onMounted(fetchData);

    return {
      showModal,
      availableOptions,
      visibleColumns,
      sortedCoins: computed(() => {
        if (!sortKey.value) {
          return coins.value;
        }
        return coins.value.slice().sort((a, b) => {
          const modifier = sortOrders[sortKey.value] === 'desc' ? -1 : 1;
          if (typeof a[sortKey.value] === 'number' && typeof b[sortKey.value] === 'number') {
            return modifier * (a[sortKey.value] - b[sortKey.value]);
          } else {
            return modifier * a[sortKey.value].toString().localeCompare(b[sortKey.value].toString(), 'en', { sensitivity: 'base' });
          }
        });
      }),
      sort,
      saveColumns
    };
  }
};
</script>

<style scoped>
.coin-image {
  width: 30px;
  height: 30px;
}

th {
  color: rgb(39, 38, 38);
  font-weight: 550;
  padding: 10px;
  cursor: pointer;
  border-top: 2px solid rgb(233, 225, 225);;
  border-bottom: 2px solid rgb(233, 225, 225);
  text-align: left;
}

td {
  padding: 10px;
  color: black;
  text-align: left;
}

.button-container {
  display: flex;
  justify-content: flex-end;
  margin-top: 20px;
  padding: 10px;
}

.Customise-button {
  padding: 10px 20px;
  border: none;

  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}
</style>
