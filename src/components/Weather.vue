<template>
  <div class="weather" v-bind:class="{ dark: darkMode, light: !darkMode }">
    <CityInput v-on:keyup="getCity" v-bind:list="cities" v-bind:model="city" />

    <div v-if="!notFoundCity" class="weather-info">
      <Title v-bind:data = data v-bind:celsius = celsius />
      <Details v-bind:data = data v-bind:celsius = celsius />
    </div>

    <div class="bottom">
      <button v-on:click="switchSystem">{{celsius ? 'Switch to Fahrenheit' : 'Switch to Celsius'}}</button>
      <button v-on:click="switchTheme">{{darkMode ? 'Switch to light' : 'Switch to dark'}}</button>
    </div>

  </div>
</template>
<script>
import './Weather.scss'
import Details from './Weather/Details'
import Title from './Weather/Title'
import CityInput from './Weather/CityInput'

export default {
  name: 'Weather',
  components: {CityInput, Details, Title},
  data () {
    return {
      city: '',
      cities: [],
      data: null,
      notFoundCity: true,
      darkMode: true,
      celsius: true,
      apiKey: '7d92ab7f4cd7150ac7def3a6fae87472'
    }
  },
  mounted () {
    document.body.className = this.darkMode ? 'dark' : 'light'
    fetch('/static/data/cities.json')
      .then(response => response.json())
      .then(data => {
        this.cities = data
      })

    navigator.geolocation.getCurrentPosition(position =>
      this.getCityWeatherByCoord(position.coords.latitude, position.coords.longitude)
    )
  },
  methods: {
    switchSystem: function () {
      this.celsius = !this.celsius
      this.getCityWeather()
    },
    switchTheme: function () {
      this.darkMode = !this.darkMode
      document.body.className = this.darkMode ? 'dark' : 'light'
    },
    getCity: function (e) {
      this.city = e.target.value
      let searchedCity = this.cities.find(v => e.target.value.toUpperCase() === v.name.toUpperCase())
      if (!searchedCity) {
        this.notFound()
      } else {
        this.getCityWeather(searchedCity.id)
      }
    },
    notFound: function () {
      this.notFoundCity = true
    },
    getCityWeatherByCoord: function (x, y) {
      let units = this.celsius ? 'metric' : 'imperial'
      let url = `https://api.openweathermap.org/data/2.5/weather?lat=${x}&lon=${y}&appid=${this.apiKey}&units=${units}`

      fetch(url)
        .then(response => response.json())
        .then(data => {
          this.city = data.name
          this.data = data
          this.notFoundCity = false
        })
    },
    getCityWeather: function (id = null) {
      if (!id) {
        let searchedCity = this.cities.find(v => this.city.toUpperCase() === v.name.toUpperCase())
        if (!searchedCity) return (this.notFoundCity = true)
        id = searchedCity.id
      }
      let units = this.celsius ? 'metric' : 'imperial'
      let url = `https://api.openweathermap.org/data/2.5/weather?id=${id}&appid=${this.apiKey}&units=${units}`

      fetch(url)
        .then(response => response.json())
        .then(data => {
          this.data = data
          this.notFoundCity = false
        })
    }
  }
}
</script>
