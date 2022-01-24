<template>
<div id="main" :class="isDay ? 'day' : 'night'">
    <div class="container my-5">
      <h1 class="title text-center">Weather App RestApi GO</h1>
      <form class="search-location" v-on:submit.prevent="getWeather">
        <input
          type="text"
          class="form-control text-muted form-rounded p-4 shadow-sm"
          placeholder="What City?"
          v-model="citySearch"
          autocomplete="off"
        />
      </form>
      <p class="text-center my-3" v-if="cityFound">No city found</p>
      <div
        class="card rounded my-3 shadow-lg back-card overflow-hidden" v-if="visible">
      <div icon="sunny" v-if="clearSky">
            <span class="sun"></span>
      </div>
      <div icon="snowy" v-if="snowy">
        <ul>
          <li></li>
          <li></li>
          <li></li>
          <li></li>
          <li></li>
          <li></li>
          <li></li>
          <li></li>
          <li></li>
          <li></li>
          <li></li>
          <li></li>
          <li></li>
        </ul>
      </div> 
        <div icon="stormy" v-if="stormy">
          <span class="cloud"></span>
          <ul>
            <li></li>
            <li></li>
            <li></li>
            <li></li>
            <li></li>
          </ul>
        </div>
        <div icon="cloudy" v-if="cloudy">
          <span class="cloud"></span>
          <span class="cloud"></span>
        </div>
        <div icon="nightmoon" v-if="clearNight">
          <span class="moon"></span>
          <span class="meteor"></span>
        </div>                     
        <!-- Top of card starts here -->
        <div class="card-top text-center" style="margin-bottom: 15rem">
          <div class="city-name my-3">
            <p>{{ weather.cityName }}</p>
            <span>...</span>
            <p class="">{{ weather.country }}</p>
          </div>
        </div>
        <!-- top of card ends here -->

        <!--card middle body, card body used cos I want to just update the innerHTML -->
        <div class="card-body">
          <!-- card middle starts here -->
          <div class="card-mid">
            <div class="row">
              <div class="col-12 text-center temp">
                <span>{{ weather.temperature }}&deg;C</span>
                <p class="my-4">{{ weather.description }}</p>
              </div>
            </div>
            <div class="row">
              <div class="col d-flex justify-content-between px-5 mx-5">
                <p>
                  <img src="./assets/down.svg" alt="" />
                  {{ weather.lowTemp }}&deg;C
                </p>
                <p>
                  <img src="./assets/up.svg" alt="" />
                  {{ weather.highTemp }}&deg;C
                </p>
              </div>
            </div>
          </div>
          <!-- card middle ends here -->

          <!-- card bottom starts here -->
          <div class="card-bottom px-5 py-4 row">
            <div class="col text-center">
              <p>{{ weather.pressure }}hPa</p>
              <span>Pressure</span>
            </div>
            <div class="col text-center">
              <p>{{ weather.humidity }}%</p>
              <span>humidity</span>
            </div>
          </div>

          <!-- card bottom ends here -->
        </div>
      </div>
    </div>
</div> 
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      citySearch: "",
      weather: {
        cityFound: false,
        visible: false,
        stormy: true,
        cloudy: false,
        clearSky: false,
        clearNight: false,
        snowy: false,
        isDay:true,
        cityName:"Banjarmasin",
        country: "ID",
        temperature:12,
        description:"Clouds everywhere",
        lowTemp: "19",
        highTemp: "30",
        pressure:"1006",
        humidity:"55"
      }
    }
  },
  methods: {
    getWeather: async function() {
      // console.log(this.citySearch)
      const baseUrl = await axios.get("http://localhost:8080/api/weather/"+this.citySearch)
      try {
        this.citySearch = ""
        this.weather.cityName = baseUrl.data.data.name
        this.weather.country = baseUrl.data.data.sys.country
        this.weather.temperature = Math.round(baseUrl.data.data.main.temp)
        this.weather.description = baseUrl.data.data.weather[0].description
        this.weather.lowTemp = Math.round(baseUrl.data.data.main.temp_Min)
        this.weather.highTemp = Math.round(baseUrl.data.data.main.temp_Max)
        this.weather.pressure = baseUrl.data.data.main.pressure
        this.weather.humidity = baseUrl.data.data.main.humidity

        const timeOfDay = baseUrl.data.data.weather[0].icon

        if(timeOfDay.includes("n")) {
          this.isDay = false
        } else {
          this.isDay = true
        }

        const mainWeather = baseUrl.data.data.weather[0].main
        if (mainWeather.includes("Clouds")) {
          this.stormy = false
          this.cloudy = true
          this.clearSky = false
          this.clearNight = false
          this.snowy = false     
        }
        if (mainWeather.includes("Thunderstrom") || mainWeather.includes("Rain")) {
          this.stormy = true
          this.cloudy = false
          this.clearSky = false
          this.clearNight = false
          this.snowy = false     
        }      
        if (mainWeather.includes("ClearSky") && this.isDay) {
          this.stormy = false
          this.cloudy = true
          this.clearSky = true
          this.clearNight = false
          this.snowy = false     
        }  
        if (mainWeather.includes("ClearSky") && !this.isDay) {
          this.stormy = false
          this.cloudy = true
          this.clearSky = false
          this.clearNight = true
          this.snowy = false     
        } 
        if (mainWeather.includes("Snow")) {
          this.stormy = false
          this.cloudy = true
          this.clearSky = true
          this.clearNight = false
          this.snowy = true     
        }                 

        this.visible = true  
        this.cityFound = false      
      } catch (error) {
        console.log(error)
        this.cityFound = true
        this.visible = false  
      }    
      
    },
  },
  
}
</script>
<style>
@import './assets/custom.css';
@import './assets/animation.css';
</style>
