<template>
  <div class="city-input">
    <input
      type = "text"
      list="cityDatalist"
      v-on:keyup="update"
      placeholder="City name"
    />
    <datalist id = "cityDatalist">
      <option
        v-bind:key="cityValue.id"
        v-for="cityValue in (list || []).filter(v => v.name !== value)"
        :value="cityValue.name"
      />
    </datalist>
  </div>
</template>

<script>
export default {
  name: 'CityInput',
  props: [ 'list', 'model' ],
  data () {
    return {
      value: ''
    }
  },
  model: {
    prop: 'model',
    event: 'keyup'
  },
  methods: {
    update: function (e) {
      this.value = e.target.value
      this.$emit('keyup', e)
    }
  }
}
</script>
