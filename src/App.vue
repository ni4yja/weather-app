<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp < 0 ? 'cold' : ''">
    <div class="app-wrap">
      <div class="search-box">
        <input 
          type="text"
          class="search-input"
          v-model="query"
          @keypress="fetchWeather"
        >
      </div>
      <div class="info-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="info-box">
          <p class="date">{{ dateBuilder() }}</p>
          <h3 class="location">{{ weather.name }}, {{ weather.sys.country }}</h3>
        </div>
        <div class="weather-box">
          <p class="temperature">{{ Math.round(weather.main.temp) }}°C</p>
          <i class="wi wi-cloudy" v-if="typeof weather.main != 'undefined' && weather.weather[0].main == 'Clouds'"></i>
          <i class="wi wi-rain" v-if="typeof weather.main != 'undefined' && weather.weather[0].main == 'Rain'"></i>
          <i class="wi wi-snow" v-if="typeof weather.main != 'undefined' && weather.weather[0].main == 'Snow'"></i>
          <p class="details">{{ weather.weather[0].main }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      api_key: 'e40b81b7b76dc2f004572ea29e16775f',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {}
    }
  },
  methods: {
    fetchWeather (e) {
      if (e.key == "Enter") {
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
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
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
      
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      
      return `${day} ${date} ${month} ${year}`;
    }
  }
}
</script>

<style lang="scss">

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.wi {
  font-size: 2rem;
  color: gray;
}

body {
  font-family: 'montserrat', sans-serif;
}

@font-face {
  font-family: "Weather Icons";
  src: url("/public/font/weathericons-regular-webfont.svg");
}

#app {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-image: url('./assets/warm-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;

  &.cold {
    background-image: url('./assets/cold-bg.jpg');
  }
}

.app-wrap {
  width: 300px;
  height: 500px;
  padding: 20px;
  border-radius: 20px;
  background: rgba(0, 0, 0, 0.3);
}

.search-box {
  margin-bottom: 50px;

  .search-input {
    display: block;
    width: 100%;
    padding: 10px;
    appearance: none;
    border:none;
    outline: none;
    background: none;
    box-shadow: 0px 0px 8px rgba(0, 0, 0, .25);
    background-color: rgba(255, 255, 255, .5);
    border-radius: 16px 0px 16px 0px;
    font-size: 1rem;
  }
}

.info-box {
  text-align: center;
  margin-bottom: 50px;

  .date {
    font-size: 1.2rem;
    color: #fff;
    margin-bottom: 10px;
  }

  .location {
    font-size: 2rem;
    color: #fff;
    font-weight: 400;
  }
}

.weather-box {
  width: 150px;
  height: 150px;
  margin: 0 auto;
  padding: 10px;
  border: 1px solid gray;
  text-align: center;
  background-color: rgba(255, 255, 255, .5);
  box-shadow: 0px 0px 8px rgba(0, 0, 0, .5);

  .temperature {
    font-size: 3rem;
    font-weight: bold;
    color: gray;
    margin-bottom: 15px;
  }

  .details {
    font-size: 1.8rem;
    color: gray;
  }
}
</style>
