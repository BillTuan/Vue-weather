<template lang="html">
  <div class="root">
    <div class="time">
      {{new Date(currentLocation.dt * 1000).toUTCString()}}
    </div>
    <div class="location">
      {{`${currentLocation.name} - ${currentLocation.sys && currentLocation.sys.country}`}}
    </div>
    <div class="image">
      <select v-model="selectLocation">
        <option
        v-for="location in locations"
        :value="location"
        :key="location">
        {{location}}
      </option>
    </select>
    </div>
    <div class="temperature">{{currentLocation.main && parseInt(currentLocation.main.temp)}}</div>
    <div class="description">
      <div v-for="(weather, index) in currentLocation.weather" :key="index">{{weather.description}}</div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data () {
    return {
      locations: ['Stockholm', 'Hanoi', 'Paris', 'London', 'Beijing'],
      selectLocation: 'Stockholm',
      currentLocation: {}
    }
  },
  mounted: async function () {
    await this.getWeather(this.selectLocation)
  },
  methods: {
    getWeather: async function (location) {
      const { data } = await axios.get(`https://api.openweathermap.org/data/2.5/find?q=${location}&units=metric&appid=dfe15a41201d660911d013203832e676`)
      this.currentLocation = data.list[0]
    }
  },
  watch: {
    selectLocation: function (newLocation) {
      this.getWeather(newLocation)
    }
  }
}
</script>

<style lang="scss">
%text {
  font-size: 25px;
  font-weight: bold;
  padding: 20px;
}
.root {
  width: 100vw;
  height: 100vh;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(3, 1fr);
  .time {
    @extend %text;
    display: flex;
    align-items: flex-end;
  }
  .location {
    @extend %text;
    grid-column-start: 1;
    grid-row-end: 4;
  }
  .image {
    grid-column: span 2;
    grid-row: span 3;
    background-image: url('../assets/weather.svg');
    background-repeat: no-repeat;
    background-position: center;
    display: flex;
    justify-content: center;
    select {
      margin-top: 30px;
      width: 180px;
    }
    select:focus {
      outline: none;
    }
  }
  .temperature {
    @extend %text;
    grid-row: span 2;
    display: flex;
    align-items: center;
    font-size: 10em;
  }
  .description {
    @extend %text;
    grid-column-start: 4;
    grid-row-end: 4;
  }
}
</style>
