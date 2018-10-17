<template>
  <v-layout
    column
    justify-center>
    <v-flex
      xs12
      sm8
      md6>
      <!-- <weather-card/> -->
      <v-card>
        <v-layout
          wrap
          align-center>
          <v-flex
            xs12
            sm6
            md8>
            <v-card-title class="headline">{{ weatherData.name }}, {{ weatherData.sys.country }}</v-card-title>
            <span class="actual-date">{{ getDayOfWeek(actualDate) }}, {{ actualDate }}</span>
          </v-flex>
          <v-flex
            xs12
            sm6
            md4
            justify-end>
            <v-text-field
              :value="searchValue"
              :hide-details="true"
              v-model="searchValue"
              append-icon="search"
              label="Location"
              solo
              @click:append="search"
              @keyup.enter="search"
            />
          </v-flex>
        </v-layout>
        <v-card-text>
          <v-container
            id="input-usage"
            grid-list-xl
            fluid
          >
            <v-layout
              wrap>

              <v-flex
                xs12
                md7>
                <p class="temperature">{{ Math.round(weatherData.main.temp) }} &deg;C</p>
                <p>Weather: {{ weatherData.weather[0].description }}</p>
                <p>Sunrise: {{ convertToTime(weatherData.sys.sunrise) }}</p>
                <p>Sunset: {{ convertToTime(weatherData.sys.sunset) }}</p>
              </v-flex>

            </v-layout>
          </v-container>
        </v-card-text>
        <v-divider/>
        <v-card-actions>
          <span
            v-for="data in forecastData"
            :key="data.dt">
            <p>{{ getDayOfWeek(data.dt_txt) }}</p>
            <p>{{ data.dt_txt }}</p>
            <p>{{ Math.round(data.main.temp) }} &deg;C</p>
            <p>{{ data.weather[0].description }}</p>
          </span>
        </v-card-actions>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
// import WeatherCard from '~/components/WeatherCard.vue'

export default {
  components: {
    // WeatherCard,
  },
  data() {
    return {
      searchValue: null,
      actualDate: new Date().toISOString().slice(0, 10),
      weekday: ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'],
    }
  },
  created() {
    this.searchValue = this.weatherData.name;
  },
  async asyncData({ app }) {
    const location = await app.$axios.$get('http://extreme-ip-lookup.com/json/');
    const data = await app.$axios.$get(`${ app.$axios.defaults.baseURL }lat=${ location.lat }&lon=${ location.lon }`);
    const { list } = await app.$axios.$get('http://api.openweathermap.org/data/2.5/forecast?appid=9c1daad0a10384a842139eee0dd55e0a&units=metric&q=Szczecin');
    const fiveDayForecast = [];
    const actualDate = new Date().toISOString().slice(0, 10);
    list.forEach(item => {
      if (item.dt_txt.slice(11, 20) === '12:00:00' && item.dt_txt.slice(0, 10) !== actualDate) {
        console.log(item);
        fiveDayForecast.push(item);
      }
    });

    return { weatherData: data, forecastData: fiveDayForecast };
  },
  methods: {
    async search() {
      this.weatherData = await this.$axios.$get(`${ this.$axios.defaults.baseURL }q=${ this.searchValue }`);
    },
    convertToTime(timestamp) {
      return new Date(timestamp * 1e3).toISOString().slice(-13, -8);
    },
    getDayOfWeek(date) {
      const newDate = new Date(date);
      return this.weekday[newDate.getDay()];
    },
  }
}
</script>

<style>
  .container {
    opacity: .9;
  }
  .v-card {
    box-shadow: none;
    padding: 20px;
  }
  .v-card__title {
    padding-bottom: 0px;
  }
  .actual-date {
    padding-left: 16px;
  }
  .v-text-field.v-text-field--solo:not(.v-text-field--solo-flat) .v-input__slot {
    box-shadow: none;
    background-color: #f6f6f6;
    border-radius: 10px;
    border-radius: 30px;
    padding-left: 20px;
    padding-right: 20px;
  }
  .temperature {
    font-size: 72px;
  }
</style>
