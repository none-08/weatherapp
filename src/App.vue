<template>
  <div class="border bg-white rounded shadow my-3 p-3">
    <h1>Weather app</h1>
    <input
      autofocus
      type="text"
      v-model="query"
      autocomplete="off"
      class="form-control my-3"
      @keypress="getWeather"
      placeholder="Which city?"
      list="cities"
    />
    <datalist id="cities">
      <option v-for="state in states" :key="state" :value="state"></option>
      <!-- <option value="Dubai"></option> -->
    </datalist>

    <h1>{{ cityFindMsg }}</h1>
    <div class="position-relative">
      <h2>{{ country }}</h2>
      <h3>{{ cityName }}</h3>
      <Video :src="video" />
      <p class="temp">{{ Math.round(temp) }}°C</p>
      <div class="d-flex justify-content-around mb-4">
        <p class="humidity">{{ humidity }}</p>
        <p class="wind">{{ Math.round(wind) }}</p>
      </div>
      <p class="describ mb-2">{{ describ }}</p>
    </div>
  </div>
</template>

<script>
import Video from "./components/videos";

export default {
  name: "App",
  components: {
    Video,
  },
  data() {
    return {
      query: "",
      states: [],
      // timeout_id: null,
      filteredStates: [],
      cityFindMsg: "What city? Search it",
      cityName: "City",
      country: "Country",
      video: "/videos/sunny.mp4",
      temp: "0",
      describ: "Condition",
      humidity: "0",
      wind: "0",
      API_key: "bf615debba274621967100716211106",
    };
  },
  methods: {
    getWeather: async function (e) {
      try {
        if (e.key == "Enter") {
          const baseURL = `https://api.weatherapi.com/v1/current.json?key=${this.API_key}&q=${this.query}&aqi=no`;
          const res = await fetch(baseURL);
          const data = await res.json();

          this.country = data.location.country;
          this.cityName = data.location.name;
          this.describ = data.current.condition.text;
          this.temp = data.current.temp_c;
          this.humidity = data.current.humidity;
          this.wind = data.current.wind_kph;
          this.cityFindMsg = "";

          // FOR VIDEOS
          if (this.describ == "Clear" || this.describ == "Sunny") {
            this.video = "/videos/sunny.mp4";
          }
          if (this.describ == "Mist" || this.describ == "Partly cloudy") {
            this.video = "/videos/cool.mp4";
          }
          if (this.describ == "Cloudy" || this.describ == "Overcast") {
            this.video = "/videos/cloudy.mp4";
          }
          if (this.describ == "Light rain" || this.describ == "Rainy") {
            this.video = "/videos/rainy.mp4";
          }
          if (this.describ == "Snowy" || this.describ == "Rainy") {
            this.video = "/videos/snowy.mp4";
          }
        }
      } catch (error) {
        this.cityFindMsg = "No city found";
        this.cityName = "City";
        this.country = "Country";
        this.video = "/videos/sunny.mp4";
        this.temp = "0";
        this.describ = "Condition";
        this.humidity = "0";
        this.wind = "0";
      }
    },

    async filterStates() {
      const baseURL = "https://restcountries.eu/rest/v2/all";
      const res = await fetch(baseURL);
      const data = await res.json();
      for (let key of Object.keys(data))
        this.states.push(data[key]["name"], data[key]["capital"]);
      // console.log(this.states);
      // let filteredStates = this.filterStates;
      // Object.keys(data).forEach(function (k) {
      //   let states = (this.states = []);
      // console.log(data);
      // });`
    },

    // async updateInput() {
    //   if (this.timeout_id) clearTimeout(this.timeout_id);
    //   this.timeout_id = setTimeout(this.filterStates, 1000);
    // },
  },
  mounted() {
    this.filterStates();
  },
};
</script>

<style>
* {
  box-sizing: border-box;
}
#app {
  margin-right: auto;
  margin-left: auto;
  padding: 0 15px;
  text-align: center;
  max-width: 500px;
}
img {
  width: 75px;
}
video {
  /* z-index: -01; */
  top: 0;
  left: 0;
  width: 100%;
}
.temp {
  font-size: 45px;
  margin: 0.5rem 0;
}
.describ {
  font-size: 25px;
  margin: 0;
}
.humidity {
  position: relative;
  font-size: 35px;
}
.humidity::after {
  content: "Humidity";
  position: absolute;
  white-space: nowrap;
  top: 90%;
  right: 0;
  font-size: 15px;
  font-weight: 700;
}
.wind {
  position: relative;
  font-size: 35px;
}
.wind::after {
  content: "Wind-kph";
  white-space: nowrap;
  position: absolute;
  top: 90%;
  left: 0;
  font-size: 15px;
  font-weight: 700;
}
</style>
