<template>
  <div id="app">
    <div class="container">
      <div class="row">
        <div class="col-md-12">
          <h1>{{appName}}</h1>
        </div>
      </div>
      <Search @triggeredSearch="handleSearch" />
      <div class="error" v-if="error">City not found</div>
      <div class="results-container">
        <div class="row">
          <div class="col-md-12">
            <CityCard v-for="city in cities" :key="city.id" :city="city" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

import Search from './components/Search.vue';
import CityCard from './components/CityCard.vue';

export default {
  name: 'App',
  data: () => ({
    appName: 'Weather App',
    cities: [],
    error: false,
  }),
  components: {
    Search,
    CityCard,
  },
  methods: {
    async handleSearch(e) {
      await axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${e}&appid=024a207944e907758a87a6ab399364a7&units=metric`)
        .then((res) => {
          if (this.error) this.error = false;
          this.cities.push(res.data);
          localStorage.setItem('cities', JSON.stringify(this.cities));
        })
        .catch(() => {
          this.error = true;
        });
    },
  },
  mounted() {
    if (localStorage.getItem('cities') === null) {
      localStorage.setItem('cities', JSON.stringify([]));
    } else {
      const savedCities = JSON.parse(localStorage.getItem('cities'));
      this.cities = savedCities;
    }
  },
};
</script>
