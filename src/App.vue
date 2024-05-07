<template>
  <div id="app">
    <div class="search-form">
      <!-- Async Search Component -->
      <SearchAutocomplete
        label="Async Search"
        description="Type to begin searching"
        :searchFunction="fetchData"
        :customLabelFunction="formatResult"
        :options="currencies"
        :selectedOptions="selectedCurrenciesAsync"
        @updateSelectedOptions="handleUpdateSelectedCurrenciesAsync"
      />
      <!-- Sync Search Component -->
      <SearchAutocomplete
        label="Sync Search"
        description="Type to begin searching"
        :options="currencies"
        :selectedOptions="selectedCurrenciesSync"
        @updateSelectedOptions="handleUpdateSelectedCurrenciesSync"
        sync
      />
    </div>
  </div>
</template>


<script>
import SearchAutocomplete from './components/SearchAutocomplete.vue';
import '@fortawesome/fontawesome-free/css/all.css';

export default {
  components: {
    SearchAutocomplete
  },
  data() {
    return {
      currencies: [
        { code: "USD", name: "United States Dollar" },
        { code: "EUR", name: "Euro" },
        { code: "JPY", name: "Japanese Yen" },
        { code: "GBP", name: "British Pound" },
        { code: "AUD", name: "Australian Dollar" },
        { code: "CAD", name: "Canadian Dollar" },
        { code: "CHF", name: "Swiss Franc" },
        { code: "CNY", name: "Chinese Renminbi" },
        { code: "SEK", name: "Swedish Krona" },
        { code: "NZD", name: "New Zealand Dollar" },
        { code: "MXN", name: "Mexican Peso" },
        { code: "SGD", name: "Singapore Dollar" },
        { code: "HKD", name: "Hong Kong Dollar" },
        { code: "NOK", name: "Norwegian Krone" },
        { code: "KRW", name: "South Korean Won" },
        { code: "TRY", name: "Turkish Lira" },
        { code: "RUB", name: "Russian Ruble" },
        { code: "INR", name: "Indian Rupee" },
        { code: "BRL", name: "Brazilian Real" },
        { code: "ZAR", name: "South African Rand" },
        { code: "PHP", name: "Philippine Peso" },
        { code: "CZK", name: "Czech Koruna" },
        { code: "PLN", name: "Polish Zloty" },
        { code: "THB", name: "Thai Baht" },
        { code: "IDR", name: "Indonesian Rupiah" },
        { code: "HUF", name: "Hungarian Forint" },
        { code: "ILS", name: "Israeli New Shekel" },
        { code: "DKK", name: "Danish Krone" },
        { code: "MYR", name: "Malaysian Ringgit" },
        { code: "ARS", name: "Argentine Peso" }
      ],
      selectedCurrenciesAsync: [],
      selectedCurrenciesSync: []
    };
  },
  methods: {
    fetchData(query) {
      return new Promise(resolve => {
        setTimeout(() => {
          const filteredItems = this.currencies.filter(currency =>
            currency.name.toLowerCase().includes(query.toLowerCase()) ||
            currency.code.toLowerCase().includes(query.toLowerCase())
          );
          resolve(filteredItems.length ? filteredItems : [{ code: 'NRF', name: 'No results found' }]);
        }, 300);
      });
    },
    formatResult(item) {
      return `${item.code} - ${item.name}`;
    },
    handleUpdateSelectedCurrenciesAsync(option) {
      this.updateSelection(this.selectedCurrenciesAsync, option);
    },
    handleUpdateSelectedCurrenciesSync(option) {
      this.updateSelection(this.selectedCurrenciesSync, option);
    },
    updateSelection(selectedArray, option) {
      const index = selectedArray.findIndex(selected => selected.code === option.code);
      if (index !== -1) {
        selectedArray.splice(index, 1);
      } else {
        selectedArray.push(option);
      }
    }
  }
};
</script>

<style>
#app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh; 
}

.search-form {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px; 
  padding: 20px;
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
  border-radius: 8px;
  background-color: #fff;
}
</style>
