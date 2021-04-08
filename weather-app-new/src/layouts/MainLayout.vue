<template>
  <q-layout class="flex column" view="lHh Lpr lFf">
    <div class="col text-center">
      <div v-if="entered">
        <InputSearch />
        <div class="col text-center">
          <h4 class="location">Bournemouth</h4>
          <h3 class="temp">8&deg;C</h3>
          <h5 class="weather-desc">Cloudy</h5>
          <img
            class="weather-img"
            src="../css/images/cloud.png"
            alt="weather"
          />
        </div>
      </div>
      <div v-else class="main-title">
        <h2 class="text-weight-light">
          Weather<br />App
          <span class="material-icons">
            thermostat
          </span>
        </h2>
      </div>
    </div>

    <q-btn class="col location-btn" flat v-on:click="enteredClick">
      <q-icon left size="3em" name="my_location" />
      <div>Find my location</div>
    </q-btn>
  </q-layout>
</template>

<script>
import InputSearch from "./InputSearch.vue";
export default {
  components: { InputSearch },
  name: "MainLayout",
  data() {
    return {
      entered: false
    };
  },
  methods: {
    enteredClick() {
      this.entered = true;
    }
  }
};
</script>

<style lang="sass" scoped>
.q-layout
  background: #808080
  background: -webkit-linear-gradient(to bottom, #3fada8, #808080)
  background: linear-gradient(to bottom, #3fada8, #808080)
  position: relative

.input
  padding: 25px
  padding-right: 50px

.main-title
  height: 50px
  padding-top: 100px
  position: relative


.col
  height: 100%
  width: 100%

.q-icon
  cursor: pointer

.weather-img
  position: relative
  width: 100px

.temp
  margin: -20px

.location-btn
  bottom: 0
</style>
