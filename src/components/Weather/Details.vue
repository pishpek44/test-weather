<template>
  <div class="weather-details">
    <div>
      <div>Current temp: <div class = "right">{{Math.round(data.main.temp)}} °{{ unit }}</div></div>
      <div>Feels like: <div class = "right">{{Math.round(data.main.feels_like)}} °{{ unit }}</div></div>
      <div>Max temp: <div class = "right">{{Math.round(data.main.temp_max)}} °{{ unit }}</div></div>
      <div>Min temp: <div class = "right">{{Math.round(data.main.temp_min)}} °{{ unit }}</div></div>
      <div>Humidity: <div class = "right">{{Math.round(data.main.humidity)}} %</div></div>
      <div>Pressure: <div class = "right">{{data.main.pressure}} mm</div></div>
      <div>Wind: <div class = "right">{{data.wind.speed}}</div></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Details',
  props: [ 'data', 'celsius' ],
  computed: {
    unit: function () { return this.celsius ? 'C' : 'F' }
  }
}
</script>
