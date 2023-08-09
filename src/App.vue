<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp >50 ?
  'warm' : ''">
    <main>
       <div class="search-box">
        <input 
        type="text" 
        class="search-bar" 
        placeholder="Search..."
        v-model="query" 
        @keypress="fetchWeather"
      />

      <!-- <button @click='varcelsius' class='uibutton'>Toggle Units</button> -->

      <button @click='convertTempuratureUnit(weather.main.temp, "C")' class='uibutton'>Test</button>
      <button @click='convertTempuratureUnit(weather.main.temp, "F")' class='uibutton'>Test</button>
       </div>

       <div class='current_location'>
        <p v-if="shouldHideCurrentLocation">Your current location is: {{ city }} in {{region}}, {{country}}.</p>
        <p :style="{color: 'red'}">{{ errorMessage }}</p>
      </div>

       <div class="weather-wrap" v-if="(typeof weather.main != 'undefined')">
        <div class="location-box">
          <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="time"></div>
          <div class="date">{{ dateBuilder() }}</div>
          <div class="humidityrating">Humidity Rating: {{ weather.main.humidity }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">
            {{ Math.round(weather.main.temp) }}°f/{{Math.round(temperatureInCelcius) }}°c </div>
          <!-- <div class="altemp">{{ Math.round(weather.main.temp)&units==metric }}°c</div> -->
          <div class="weather">{{ weather.weather[0].main }}</div>

          <!-- <div class="weather2">{{Math.round(temperatureInCelcius) }}°c</div> -->
        </div>
       </div>
       <div class="error" v-else>
        There is an error
       </div>
       <!-- <p>Computed shouldHideCurrentLocation: {{ shouldHideCurrentLocation }}</p>
       <p>City: {{ city }}</p>
       <p>Query: {{ query }}</p> -->
    </main>
  </div>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      api_key:'9f64957c4b61fbedf776bf8341db1d70',
      url_base:'https://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {},
      city: '',
      region: '',
      country: '',
      errorMessage: '',
      temperatureInCelcius: '',
      temperatureInF: ''
    }
  },
  methods: {
    fetchWeather (e) {
      if (e.key == "Enter") {
        fetch( `${this.url_base}weather?q=${this.query}&units=imperial&APPID=${this.api_key}`)
          .then(res => {
            return res.json();
          }).then(this.setResults);
      }
    },
    setResults (results) {
      this.weather = results;
    },
    dateBuilder () {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", 
      "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", 
      "Saturday"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    },

    async getGeolocationInformation() {
      const API_KEY = '15a3076d332a4c8f85bddf4c6dbf7b7d';
      const API_URL = 'https://ipgeolocation.abstractapi.com/v1/?api_key=' + API_KEY;
      try {
        this.errorMessage = '';
        const apiResponse = await fetch(API_URL);
        const data = await apiResponse.json();
        const {city, country, region, } = data;
        this.city = city;
        this.region = region;
        this.country = country;
      } catch (error) {
        this.errorMessage = error.message;
      }
    },
    convertTempuratureUnit(value, toUnit) {
      // from F to C
      if (toUnit === 'C') {
        console.info((value-32)/1.8)
        this.temperatureInCelcius = (value-32)/1.8
        return (value-32)/1.8
      } else if (toUnit === 'F') {
        // from C to F
        this.temperatureInF = value*1.8 + 32
        console.info(value*1.8 + 32)
        return value*1.8 + 32
      }
    }
 },
 computed: {
      shouldHideCurrentLocation () {
        return this.city.split(" ").join("") !== this.query.split(" ").join("").toLowerCase()
    }
  },
 mounted () {
  this.getGeolocationInformation();
  }
}
</script>

<style>
* {
  margin:0;
  padding:0;
  box-sizing: border-box;

}

body {
  font-family: 'montserat', sans-serif;
}
#app {
  background-image: url('./assets/pngtree-blue-snowflake-standing-background-format-picture-image_953419.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

#app.warm {
  background-image: url('./assets/doug_pic_16_png-4.jpeg');
}
main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;

  color: #313131 ;
  font-size: 20px;

  appearance: none;
  border: none;
  outline: none;
  background: none;

  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0,0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}
.location-box .location {
  color: #FFF;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #FFF;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box {
  text-align: center;
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #FFF; 
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color:#FFF;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.error {
  text-align: center;
  color:rgb(249, 246, 246);
  font-size: 48px;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  display: none;
}

.uibutton {
  background-color: #596a81; /* Green */
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  font-style: montserat;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  
}

.uibutton:hover {
  background-color: rgb(114, 166, 229);
}
.weather-box.altemp {
  display: none;
}

.location-box .humidityrating {
  color: #FFF;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.current_location {
  color: #FFF;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
  display: block;
}

.weather-box .weather2 {
  display: inline-block;
  padding: 10px 25px;
  color: #FFF; 
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>


