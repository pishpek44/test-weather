<template>
  <div class="weather" v-bind:class="{ dark: darkMode, light: !darkMode }">
    <div class="city-input">
      <label>
        <input type = "text" list="cityDatalist" v-on:keyup="getCity" v-model="city" placeholder="City name" />
      </label>
    </div>

    <datalist id = "cityDatalist">
      <option v-bind:key="cityValue.id" v-for="cityValue in cities" :value="cityValue.name" />
    </datalist>

    <div v-if="!notFoundCity" class="weather-info">

      <div class="weather-title">
        <div>
          <h1>
            {{Math.round(data.main.temp)}}°{{celsius?'C':'F'}}
          </h1>
        </div>
        <img :src = "`http://openweathermap.org/img/wn/${data.weather[0].icon}@4x.png`"  alt=""/>
        <div>
          <h2>
            {{data.name}}
          </h2>
        </div>
      </div>

      <div class="weather-details">
        <div>
          <div>Current temp: <div class = "right">{{Math.round(data.main.temp)}} °{{celsius?'C':'F'}}</div></div>
          <div>Feels like: <div class = "right">{{Math.round(data.main.feels_like)}} °{{celsius?'C':'F'}}</div></div>
          <div>Max temp: <div class = "right">{{Math.round(data.main.temp_max)}} °{{celsius?'C':'F'}}</div></div>
          <div>Min temp: <div class = "right">{{Math.round(data.main.temp_min)}} °{{celsius?'C':'F'}}</div></div>
          <div>Humidity: <div class = "right">{{Math.round(data.main.humidity)}} %</div></div>
          <div>Pressure: <div class = "right">{{data.main.pressure}} mm</div></div>
          <div>Wind: <div class = "right">{{data.wind.speed}} mps</div></div>
        </div>
      </div>
    </div>

    <div class="bottom">
      <button v-on:click="switchSystem">{{celsius ? 'Switch to Fahrenheit' : 'Switch to Celsius'}}</button>
      <button v-on:click="switchTheme">{{darkMode ? 'Switch to light' : 'Switch to dark'}}</button>
    </div>

  </div>
</template>
<script>
import './Weather.scss'

export default {
  name: 'Weather',
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
      let metric = this.celsius ? '&units=metric' : ''
      let url = `https://api.openweathermap.org/data/2.5/weather?lat=${x}&lon=${y}&appid=${this.apiKey}${metric}`

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
      let metric = this.celsius ? '&units=metric' : ''
      let url = `https://api.openweathermap.org/data/2.5/weather?id=${id}&appid=${this.apiKey}${metric}`

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
