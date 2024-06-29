<template>
  <div class="sidebar">
    <form class="search-form" action="">
      <div class="search-form__block">
        <label for="search-input">Введите имя сотрудника:</label>
        <input @input="handleSearch" v-model="searchInput" placeholder="Введите Id или имя" type="text"
          id="search-input" name="search">
      </div>
      <div class="search-form__block">
        <label for="search-result">Результаты</label>
        <div v-if="isLoading" class="search-result_preloader">
          Загружка...
        </div>
        <ul class="search-result" id="search-result" v-else-if="filteredResults.length > 0">
          <li class="search-result__element" v-for="result in filteredResults" :key="result.id"
            @click="selectResult(result)" :class="{ chosen: result === selectedResult, selected: result.selected }">
            <div class="search-result__img">
              <img :src="result.img" alt="Фото" v-if="result.img">
              <img src="./img/gag-img_small.png" alt="Фото" v-else>
            </div>
            <div class="search-result__info">
              <span class="search-result__name">{{ result.name }}</span>
              <span class="search-result__mail">{{ result.email }}</span>
            </div>
          </li>
        </ul>
        <span class="nothing-found" v-else>Ничего не найдено</span>
      </div>
    </form>
  </div>
</template>
<script>
import { mapState, mapActions } from 'vuex';
export default {
  name: 'SideBar',

  data() {
    return {
      searchInput: '',
    };
  },
  created() {
    this.fetchResults();
  },
  methods: {
    ...mapActions(['fetchResults']),
    handleSearch() {
      let searchTerms = this.searchInput.split(',').map(term => term.trim().toLowerCase());

      if (this.searchInput === '' || !searchTerms.length) {
        this.$store.commit('setFilteredResults', []);
        this.$store.commit('setSelectedResult', null);
      } else {
        this.$store.commit('setIsLoading', true);
        let filteredResults = this.$store.state.results.filter(result => {

          return searchTerms.some(term => {
            return result.name.toLowerCase().includes(term) || result.id.toString().includes(term);
          });
        });
        this.$store.commit('setIsLoading', false);
        this.$store.commit('setFilteredResults', filteredResults);
      }
    }

    ,
    selectResult(result) {
      this.$store.commit('setSelectedResult', result);
      this.$store.state.results.forEach(item => {
        item.selected = false;
      });

      result.selected = true;

      this.$emit('search-results', result);
    }
  },
  computed: {
    ...mapState(['results', 'filteredResults', 'searchInput']),
  },
};
</script>
<style scoped lang="scss">
$primary-color: #000000;
$secondary-color: #76787D;

.sidebar {
  padding: 27px 30px 30px 20px;
}

.search-form {
  display: flex;
  flex-direction: column;
  row-gap: 22px;
}

.search-form__block {
  display: flex;
  flex-direction: column;
  row-gap: 22px;
  align-items: flex-start;

  label {
    font-size: 16px;
    font-weight: 600;
    line-height: 22px;
    color: #333;
  }

  input {
    border: 1px solid #E9ECEF;
    border-radius: 16px;
    padding: 14px 16px;
    color: $primary-color;

  }

  input:focus {
    outline: none;
    border-radius: 0px;
    border-color: #a8a8a8;
  }
}

.nothing-found {
  font-size: 14px;
  font-weight: 400;
  line-height: 17px;
  color: $secondary-color;
}

.search-result {
  margin: 0;
  padding: 0;
  list-style: none;
  display: flex;
  align-items: flex-start;
  flex-direction: column;
  row-gap: 18px;

  &__element {
    box-shadow: 0px 0px 10px 0px #0000001A;
    width: 240px;
    height: 70px;
    max-width: 100%;
    border-radius: 10px;
    overflow: hidden;
    display: grid;
    grid-template-columns: 70px 1fr;
    ;
  }

  &__img {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 70px;
    height: 70px;

    img {
      height: 100%;
      width: 100%;
      object-fit: cover;
    }
  }

  &__info {
    display: flex;
    align-items: flex-start;
    flex-direction: column;
    row-gap: 5px;
    padding: 15px;
  }

  &__name {
    font-size: 14px;
    font-weight: 600;
    line-height: 17px;
    color: #333;
    height: 17px;
    width: 140px;
    -ms-text-overflow: ellipsis;
    -o-text-overflow: ellipsis;
    text-overflow: ellipsis;
    overflow: hidden;
    -ms-line-clamp: 1;
    -webkit-line-clamp: 1;
    line-clamp: 1;
    display: -webkit-box;
    display: box;
    word-wrap: break-word;
    -webkit-box-orient: vertical;
    box-orient: vertical;
  }

  &__mail {
    font-size: 14px;
    font-weight: 400;
    line-height: 17px;
    color: #76787D;
    height: 17px;
    width: 140px;
    -ms-text-overflow: ellipsis;
    -o-text-overflow: ellipsis;
    text-overflow: ellipsis;
    overflow: hidden;
    -ms-line-clamp: 1;
    -webkit-line-clamp: 1;
    line-clamp: 1;
    display: -webkit-box;
    display: box;
    word-wrap: break-word;
    -webkit-box-orient: vertical;
    box-orient: vertical;
  }
}

.selected {
  background-color: lightblue;
}

@media(max-width:1024px) {
  .sidebar {
    padding: 0;
  }

  .search-result {
    &__element {
      width: 100%;
    }
  }
}
</style>
