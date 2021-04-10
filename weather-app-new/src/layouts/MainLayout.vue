<template>
  <q-layout class="flex column" v-bind:class="bgClass" view="lHh Lpr lFf">
    <div class="col text-center">
      <div v-if="entered">
        <div class="col text-center input">
          <q-input
            color="black"
            v-model="search"
            label="Search"
            v-on:keyup.enter="getWeatherBySearch"
          >
            <!-- v-model="search" -->
            <template v-slot:before>
              <q-icon name="locaton_on" />
            </template>
            <template v-slot:append>
              <q-btn
                round
                dense
                flat
                icon="search"
                v-on:click="getWeatherBySearch"
              />
            </template>
          </q-input>
        </div>
        <div class="col text-center">
          <div v-if="locationOff">
            <p>Location service is turned off.<br />Try searching instead.</p>
          </div>
          <div v-if="error">
            <p>There seems to be an error.<br />Try again.</p>
          </div>
          <p v-if="isLoading">Loading...</p>
          <div v-if="!isLoading && !locationOff">
            <!-- requestSent || !locationOff && ! -->
            <h4 class="location">{{ location }}</h4>
            <h3 class="temp">{{ temp }}&deg;C</h3>
            <h5 class="weather-desc">{{ weatherDesc }}</h5>
            <img class="weather-img" v-bind:src="imgUrl" alt="weather" />
            <h6 class="h6">Time Zone:</h6>
            <p>{{ timeInfo }}</p>
          </div>
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

    <q-btn class="col location-btn" flat v-on:click="getWeatherByCoords">
      <q-icon left size="3em" name="my_location" />
      <div>Find my location</div>
    </q-btn>
  </q-layout>
</template>

<script>
export default {
  name: "MainLayout",
  data() {
    return {
      isLoading: null,
      requestSent: null,
      search: "",
      entered: false,
      locationOff: null,
      error: null,
      lat: null,
      lon: null,
      temp: "",
      weatherDesc: "",
      location: "",
      apiUrl: "https://api.openweathermap.org/data/2.5/weather",
      
      weatherData: null,
      imgCode: null,
      imgUrl: null,
      timeZone: null,
      timeInfo: null
    };
  },
  computed: {
    bgClass() {
      if (this.weatherData) {
        if (this.imgCode.endsWith("n")) {
          return "bg-night";
        } else {
          return "bg-day";
        }
      } else {
        return null;
      }
    }
  },
  methods: {
    getWeatherByCoords() {
      this.$q.loading.show();
      navigator.geolocation.watchPosition(
        position => {
          navigator.geolocation.getCurrentPosition(position => {
            this.lat = position.coords.latitude;
            this.lon = position.coords.longitude;
            this.$axios(
              `${this.apiUrl}?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric`
            )
              .then(response => {
                this.weatherData = response.data;
                this.location = this.weatherData.name;
                this.weatherDesc = this.weatherData.weather[0].description;
                this.temp = Math.round(this.weatherData.main.temp);
                this.timeZone = this.weatherData.timezone;
                const time = new Date();
                const localTime = time.getTime();
                const localOffset = time.getTimezoneOffset() * 60000;
                const utc = localTime + localOffset;
                const searchedLocationTime = utc + 1000 * this.timeZone;
                const locationTime = new Date(searchedLocationTime);
                this.timeInfo = locationTime;
                this.imgCode = this.weatherData.weather[0].icon;
                this.imgUrl = `http://openweathermap.org/img/wn/${this.imgCode}@2x.png`;
                this.$q.loading.hide();
                this.entered = true;
              })
              .catch(error => {
                if (error.response) {
                  console.log("1 " + error.response.data);
                  console.log("2 " + error.response.status);
                  console.log("3 " + error.response.headers);
                  this.error = true;
                  this.entered = true;
                } else if (error.request) {
                  this.error = true;
                  console.log("4 " + error.request);
                } else {
                  this.error = true;
                  console.log("Error", error.message);
                }
                this.error = true;
                this.entered = true;
                console.log("5 " + error.config);
              });
          });
        },
        error => {
          if (error.code == error.PERMISSION_DENIED) 
          this.$q.loading.hide();
          this.locationOff = true;
          this.requestSent = false;
          this.entered = true;
        }
      );
      // navigator.geolocation.getCurrentPosition(position => {
      //   this.lat = position.coords.latitude;
      //   this.lon = position.coords.longitude;
      //   this.entered = true;
      //   this.getWeatherByCoords();
      // });
    },
    // getWeatherByCoords() {
    //   this.$q.loading.show();
    //   this.$axios(
    //     `${this.apiUrl}?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric`
    //   )
    //     .then(response => {
    //       this.weatherData = response.data;
    //       this.location = this.weatherData.name;
    //       this.weatherDesc = this.weatherData.weather[0].description;
    //       this.temp = Math.round(this.weatherData.main.temp);
    //       this.timeZone = this.weatherData.timezone;
    //       const time = new Date();
    //       const localTime = time.getTime();
    //       const localOffset = time.getTimezoneOffset() * 60000;
    //       const utc = localTime + localOffset;
    //       const searchedLocationTime = utc + 1000 * this.timeZone;
    //       const locationTime = new Date(searchedLocationTime);
    //       this.timeInfo = locationTime;
    //       this.imgCode = this.weatherData.weather[0].icon;
    //       this.imgUrl = `http://openweathermap.org/img/wn/${this.imgCode}@2x.png`;
    //       this.$q.loading.hide();
    //       this.entered = true;
    //     })
    //     .catch(error => {
    //       if (error.response) {
    //         console.log("1 " + error.response.data);
    //         console.log("2 " + error.response.status);
    //         console.log("3 " + error.response.headers);
    //         this.error = true;
    //         this.entered = true;
    //         // this.error = true;
    //       } else if (error.request) {
    //         this.error = true;
    //         console.log("4 " + error.request);
    //       } else {
    //         this.error = true;
    //         console.log("Error", error.message);
    //       }
    //       this.error = true;
    //       this.entered = true;
    //       console.log("5 " + error.config);
    //     });
    // },
    getWeatherBySearch() {
      this.error = false;
      this.requestSent = true;
      this.locationOff = false;
      this.isLoading = true;
      this.$axios(
        `https://api.openweathermap.org/data/2.5/weather?q=${this.search}&appid=${this.apiKey}&units=metric`,
        { timeout: 2000 }
      )
        .then(response => {
          this.weatherData = response.data;
          this.location = this.weatherData.name;
          this.weatherDesc = this.weatherData.weather[0].description;
          this.temp = Math.round(this.weatherData.main.temp);
          this.imgCode = this.weatherData.weather[0].icon;
          this.imgUrl = `http://openweathermap.org/img/wn/${this.imgCode}@2x.png`;
          this.timeZone = this.weatherData.timezone;
          const time = new Date();
          const localTime = time.getTime();
          const localOffset = time.getTimezoneOffset() * 60000;
          const utc = localTime + localOffset;
          const searchedLocationTime = utc + 1000 * this.timeZone;
          const locationTime = new Date(searchedLocationTime);
          this.timeInfo = locationTime;
          this.search = "";
          this.isLoading = false;
        })
        .catch(error => {
          if (error.response) {
            console.log("1 " + error.response.data);
            console.log("2 " + error.response.status);
            console.log("3 " + error.response.headers);
            this.error = true;
          } else if (error.request) {
            this.error = true;
            console.log("4 " + error.request);
          } else {
            this.error = true;
            console.log("Error", error.message);
          }
          this.error = true;
          this.entered = true;
          console.log("5 " + error.config);
        });
    }
  }
};
</script>

<style lang="sass" scoped>
.q-layout
  background: #134E5E
  background: -webkit-linear-gradient(to top, #71B280, #134E5E)
  background: linear-gradient(to top, #71B280, #134E5E)
  &.bg-night
    background: #283048
    background: -webkit-linear-gradient(to top, #283048, #859398)
    background: linear-gradient(to top, #283048, #859398)

  &.bg-day
    background: #FF5F6D
    background: -webkit-linear-gradient(to bottom, #FFC371, #FF5F6D)
    background: linear-gradient(to bottom, #FFC371, #FF5F6D)


.weather-desc
  margin-bottom: 5px

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
  width: 150px
  margin: 5px

.temp
  margin: -30px 0

.location-btn
  position: fixed
  height: 150px
  bottom: 0

.h6
  margin: 0
</style>
