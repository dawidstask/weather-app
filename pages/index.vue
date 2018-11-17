<template>
  <v-layout
    column
    justify-center>
    <v-flex
      xs12
      sm8
      md6>
      <weather-card 
        :weather-data="weatherData" 
        :forecast-data="forecastData"/>
    </v-flex>
  </v-layout>
</template>

<script>
import WeatherCard from '~/components/WeatherCard.vue'

export default {
  components: {
    WeatherCard,
  },
  async asyncData({ app }) {
    const location = await app.$axios.$get('http://extreme-ip-lookup.com/json/');
    const data = await app.$axios.$get(`${ app.$axios.defaults.baseURL }lat=${ location.lat }&lon=${ location.lon }`);
    const { list } = await app.$axios.$get('http://api.openweathermap.org/data/2.5/forecast?appid=9c1daad0a10384a842139eee0dd55e0a&units=metric&q=Szczecin');
    const fiveDayForecast = [];
    const actualDate = new Date().toISOString().slice(0, 10);

    list.forEach(item => {
      const itemDate = item.dt_txt.slice(11, 20);
      const itemTime = item.dt_txt.slice(0, 10);

      if (itemDate === '12:00:00' && itemTime !== actualDate) {
        console.log(item);
        fiveDayForecast.push(item);
      }
    });

    return { weatherData: data, forecastData: fiveDayForecast };
  },
}
</script>
