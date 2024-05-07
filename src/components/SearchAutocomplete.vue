<template>
  <div class="search-autocomplete">
    <label>{{ label }}</label>
    <div class="search-container">
      <i class="fas fa-search search-icon"></i>
      <input type="text"
             class="search-input"
             :placeholder="description"
             @input="handleInput"
             @focus="handleFocus"
             @keydown="handleKeydown" />
    </div>
    <div v-if="showDropdown" class="results-container">
      <ul class="results-list">
        <li v-for="(result, index) in filteredResults" :key="index"
            @click="selectOption(result)"
            :class="{'selected-option': isSelected(result), 'highlighted': index === highlightedIndex}">
          <input type="checkbox" :checked="isSelected(result)" @click.stop>
          <span class="currency-code">{{ result.code }}</span>
          <span class="currency-description">{{ result.name }}</span>
        </li>
      </ul>
      <div v-if="filteredResults.length === 0" class="no-results">No results found</div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    label: String,
    description: String,
    options: Array,
    selectedOptions: Array,
    searchFunction: Function,
    sync: Boolean
  },
  data() {
    return {
      query: '',
      filteredResults: [],
      showDropdown: false,
      highlightedIndex: 0
    };
  },
  mounted() {
    document.addEventListener('click', this.handleClickOutside);
  },
  beforeDestroy() {
    document.removeEventListener('click', this.handleClickOutside);
  },
  methods: {
    handleInput(event) {
      this.query = event.target.value;
      if (this.searchFunction && !this.sync) {
        this.fetchData();
      } else {
        this.filterResults();
      }
    },
    handleFocus() {
      if (this.sync) {
        this.filterResults();
      }
    },
    handleKeydown(event) {
      if (event.key === 'ArrowDown') {
        event.preventDefault();
        if (this.highlightedIndex < this.filteredResults.length - 1) {
          this.highlightedIndex++;
        }
      } else if (event.key === 'ArrowUp') {
        event.preventDefault();
        if (this.highlightedIndex > 0) {
          this.highlightedIndex--;
        }
      } else if (event.key === 'Enter') {
        event.preventDefault();
        this.selectOption(this.filteredResults[this.highlightedIndex]);
      } else if (event.key === 'Escape') {
        event.preventDefault();
        this.showDropdown = false;
      }
    },
    fetchData() {
      if (this.searchFunction) {
        this.searchFunction(this.query).then(results => {
          this.filteredResults = results;
          this.showDropdown = true;
          this.highlightedIndex = 0;
        });
      }
    },
    filterResults() {
      this.filteredResults = this.options.filter(option =>
        option.name.toLowerCase().includes(this.query.toLowerCase()) ||
        option.code.toLowerCase().includes(this.query.toLowerCase())
      );
      this.showDropdown = this.filteredResults.length > 0;
      this.highlightedIndex = 0;
    },
    selectOption(option) {
      this.$emit('updateSelectedOptions', option);
      this.showDropdown = false;
    },
    isSelected(option) {
      return this.selectedOptions.some(selected => selected.code === option.code);
    },
    handleClickOutside(event) {
      if (!this.$el.contains(event.target)) {
        this.showDropdown = false;
      }
    }
  }
};
</script>

<style scoped>

.search-autocomplete {
  position: relative;
  max-width: 300px;
  margin: auto;
}

.search-container {
  position: relative;
  display: flex;
  align-items: center;
  width: 100%;
}

.search-input {
  width: 100%;
  padding: 10px 10px 10px 40px; 
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 16px; 
}

.search-icon {
  position: absolute;
  left: 10px; 
  top: 50%;
  transform: translateY(-50%);
  color: #888; 
  font-size: 16px; 
  pointer-events: none; 
}

.results-container {
  position:absolute;
  width: 100%;
  background: white;
  border: 1px solid #ccc;
  z-index: 10;
}

.results-list {
  list-style: none;
  margin: 0;
  padding: 0;
}

.results-list li {
  padding: 10px;
  cursor: pointer;
  display: flex;
  align-items: center;
}

.selected-option {
  background-color: #007bff;
  color: white;
}

.no-results {
  padding: 10px;
  color: #888;
}

.currency-code {
  font-weight: bold;
  margin-right: 5px;
}

.currency-description {
  color: #555;
}

input[type="checkbox"] {
  margin-right: 10px;
}

.highlighted {
  background-color: #efefef; 
}
.selected-option {
  background-color: #007bff;
  color: white;
}

</style>
