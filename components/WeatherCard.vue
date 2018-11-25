<template>
  <v-card>
    <v-layout
      wrap
      align-center>
      <v-flex
        xs12
        sm6
        md8>
        <v-card-title class="headline">
          {{ data.name }}, {{ data.sys.country }}
        </v-card-title>
        <span class="actual-date">
          {{ getDayOfWeek(actualDate) }}, {{ actualDate }}
        </span>
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
          label="Select location"
          solo
          @click:append="search"
          @keyup.enter="search"/>
      </v-flex>
    </v-layout>
    <v-card-text>
      <v-container
        id="input-usage"
        grid-list-xl
        fluid>
        <v-layout
          wrap>

          <v-flex
            xs12
            md7>
            <p class="temperature">{{ Math.round(data.main.temp) }} &deg;C</p>
            <p><img :src="getIconPath(data.weather[0].icon)"></p>
            <p>Weather: {{ data.weather[0].description }}</p>
            <p>Sunrise: {{ convertToTime(data.sys.sunrise) }}</p>
            <p>Sunset: {{ convertToTime(data.sys.sunset) }}</p>
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
        <p><img :src="getIconPath(data.weather[0].icon)"></p>
        <p>{{ data.weather[0].description }}</p>
      </span>
    </v-card-actions>
  </v-card>
</template>

<script>
export default {
  props: {
    weatherData: {
      type: Object,
      required: true,
    },
    forecastData: {
      type: Array,
      required: true,
    },
    actualDate: {
      type: String,
      required: true,
    }
  },
  data() {
    return {
      data: this.weatherData,
      searchValue: null,
      weekday: ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'],
      iconMap: {
        '01d': 'sun',
        '02d': 'cloudy',
        '03d': 'clouds',
        '04d': 'clouds',
        '09d': 'rain-1',
        '10d': 'rain',
        '11d': 'rain-2',
        '13d': 'snow',
        '50d': 'clouds-1',
        '01n': 'moon-2',
        '02n': 'cloud',
        '03n': 'cloudy-2',
        '04n': 'moon',
        '09n': 'night-rain',
        '10n': 'night-rain',
        '11n': 'rain-2',
        '13n': 'snow',
        '50n': 'moon-1'
      },
    }
  },
  created() {
    this.searchValue = this.data.name;
  },
  methods: {
    async search() {
      this.data = await this.$axios.$get(`${ this.$axios.defaults.baseURL }q=${ this.searchValue }`);
    },
    convertToTime(timestamp) {
      return new Date(timestamp * 1e3).toISOString().slice(-13, -8);
    },
    getDayOfWeek(date) {
      const newDate = new Date(date);
      return this.weekday[newDate.getDay()];
    },
    getIconPath(iconCode) {
      return `/icons/svg/${ this.iconMap[iconCode] }.svg`;
    }
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
  .v-card__actions {
    justify-content: space-between;
  }
  .actual-date {
    padding-left: 16px;
    opacity: 0.7;
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
  img {
    height: 120px;
  }
</style>
