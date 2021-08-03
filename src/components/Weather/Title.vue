<template>
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
</template>

<script>
export default {
  name: 'Title',
  props: [ 'data', 'celsius' ]
}
</script>
