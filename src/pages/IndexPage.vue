<template>
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input
        @keyup.enter="getWetherBySearch"
        v-model="search"
        placeholder="Rechercher"
        dark
        borderless
      >
        <template v-slot:before>
          <q-icon @click="getLocation" name="my_location" />
        </template>

        <template v-slot:append>
          <q-btn @click="getWetherBySearch" round dense flat icon="search" />
        </template>
      </q-input>
    </div>

    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">{{ weatherData.name }}</div>
        <div class="text-h6 text-weight-light">
          {{ weatherData.weather[0].main }}
        </div>
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>{{ Math.round(weatherData.main.temp) }}</span
          ><span class="text-h4 relative-position degree">&deg;C</span>
        </div>
      </div>
      <div class="col text-center">
        <img
          :src="`https://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`"
        />
      </div>
    </template>
    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">Météo</div>
      </div>
      <q-btn @click="getLocation" class="col text-white" flat>
        <q-icon left size="3em" name="my_location" />
        <div>Trouver ma position</div>
      </q-btn>
    </template>

    <div class="col skyline">
      <img src="https://www.fillmurray.com/100/100" />
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "IndexPage",
  data() {
    return {
      search: "",
      weatherData: null,
      lat: null,
      lon: null,
      apiKey: "a51e1681bfb60b017e995e17e15a2b92",
      apiUrl: "https://api.openweathermap.org/data/2.5/weather",
    };
  },
  computed: {
    bgClass() {
      if (this.weatherData && this.weatherData.weather[0].icon.endsWith("n")) {
        return "bg-night";
      } else {
        return "bg-day";
      }
    },
  },
  methods: {
    getLocation() {
      // this.$q.loading.show();
      navigator.geolocation.getCurrentPosition((position) => {
        console.log("position:", position);
        this.lat = position.coords.latitude;
        this.lon = position.coords.longitude;
        this.getWeatherByCoords();
      });
    },
    getWeatherByCoords() {
      // this.$q.loading.show();
      this.$axios(
        `${this.apiUrl}?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric`
      ).then((response) => {
        this.weatherData = response.data;
        // this.$q.loading.hide();
      });
    },

    getWetherBySearch() {
      // this.$q.loading.show();
      console.log("getWetherBySearch");
      this.$axios(
        `${this.apiUrl}?q=${this.search}&appid=${this.apiKey}&units=metric`
      ).then((response) => {
        this.weatherData = response.data;
        // this.$q.loading.hide();
      });
    },
  },
});
</script>
<style lang="scss">
.q-page {
  background: linear-gradient(to bottom, #136a8a, #267871);
}

.q-page.bg-night {
  background: linear-gradient(to bottom, #232526, #414345);
}
.q-page.bg-day {
  background: linear-gradient(to bottom, #00b4db, #0083b0);
}
.degree {
  top: -44px;
}

.skyline {
  top: 0;
  left: 0;
  right: 0;
  height: 100px;
  background-image: url("../assets/m1.png");
  background-size: contain;
  background-position: center bottom;
}
</style>


