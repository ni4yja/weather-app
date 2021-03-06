<template>
  <div v-if="isDone">
    <div :class="{cold: isCold}">
      <div id="app" :class="[isCold ? 'cold' : '']">
        <div class="app-wrap">
          <div class="search-box">
            <input 
              type="text"
              class="search-input"
              v-model="query"
              @keypress.enter="fetchWeather"
            >
          </div>
          <div class="info-wrap" v-if="typeof weather.city != 'undefined'">
            <div class="info-box">
              <p class="date">{{ dateBuilder() }}</p>
              <h3 class="location">{{ weather.city.name }}, {{ weather.city.country }}</h3>
            </div>
            <div class="weather-box">
              <p class="temperature">{{ Math.round(weather.list[0].main.temp) }}°C</p>
              <i class="wi wi-day-sunny" v-if="getCurrent === weatherStatuses.Clear"></i>
              <i class="wi wi-cloudy" v-if="getCurrent === weatherStatuses.Clouds"></i>
              <i class="wi wi-rain"  v-if="getCurrent === weatherStatuses.Rain"></i>
              <i class="wi wi-snow"  v-if="getCurrent === weatherStatuses.Snow"></i>
              <p class="details">{{ getCurrent }}</p>
            </div>
            <div class="forecast-box">
              <div v-for="(image, index) in forecastImages" :key="index">
                <p>{{ forecastDays[index] }}</p>
                <p>{{ forecastWeather[index] }}°C</p>
                <p><img :src="getUrl[index]"/></p>
                <p>{{ forecastDetails[index] }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment';
export default {
  name: 'App',
  data() {
    return {
      api_key: process.env.VUE_APP_API_KEY,
      url_base: process.env.VUE_APP_API_URL,
      query: '',
      weather: {},
      forecastCount: 4,
      isDone: false,
      isCold: false
    }
  },
  created() {
    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition(
        position => {
          fetch(`${this.url_base}forecast?lat=${position.coords.latitude}&lon=${position.coords.longitude}&units=metric&APPID=${this.api_key}&cnt=5`)
            .then(res => {
              return res.json();
            }).then(this.setResults);
        }
      );
    }
    else{
      console.log("Your browser does not support geolocation API")
    }
  },
  computed: {
    getCurrent() {
      let detail = this.weather.list[0].weather[0].main;
      return detail;
    },
    weatherStatuses() {
      let status = {
        Clear: 'Clear',
        Clouds: 'Clouds',
        Rain: 'Rain',
        Snow: 'Snow'
      }

      return status;
    },
    forecastDays() {
      let days = [];
      for (let i = 1; i <= this.forecastCount; i++) {
        days.push(moment().add(i,'days').format("ddd"));
      }
      return days;
    },
    forecastWeather() {
      let temperature = [];
      for (let i = 1; i <= this.forecastCount; i++) {
        temperature.push(Math.round(this.weather.list[i].main.temp));
      }
      return temperature;
    },
    forecastImages() {
      let images = [];
      for (let i = 1; i <= this.forecastCount; i++) {
        images.push(this.weather.list[i].weather[0].icon);

      }
      return images;
    },
    getUrl() {
      let url = [];
      for (let i = 0; i < this.forecastImages.length; i++) {
       url.push('http://openweathermap.org/img/w/' + this.forecastImages[i] + '.png');
      }
      return url;
    },
    forecastDetails() {
      let details = [];
      for (let i = 1; i <= this.forecastCount; i++) {
        details.push(this.weather.list[i].weather[0].main);
      }
      return details;
    }
  },
  methods: {
    fetchWeather () {
      fetch(`${this.url_base}forecast?q=${this.query}&units=metric&APPID=${this.api_key}&cnt=5`)
        .then(res => {
          return res.json();
        }).then(this.setResults);
    },
    setResults (results) {
      this.weather = results;
      this.isCold = this.weather.list[0].main.temp < 0 ? true : false;
      this.isDone = true;
    },
    dateBuilder () {
      let date = moment().format('MMMM Do YYYY');
      return date;
    }
  }
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@100;400;900&display=swap');

$main-bg: #22264b;
$main-text: #e8edf3;
$highlight: #e6cf8b;

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Roboto', sans-serif;
  font-weight: 400;
}

.wi {
  font-size: 5rem;
  margin-bottom: .8rem;
  color: $highlight;
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
  width: 21rem;
  min-height: 35rem;
  padding: 2rem;
  border-radius: .2rem;
  background: $main-bg;
  transition: 0.4s;
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
    color: $main-bg;
  }
}

.info-box {
  text-align: center;
  margin-bottom: 2rem;

  .date {
    font-weight: 100;
    font-size: 1.3rem;
    color: $main-text;
    margin-bottom: 1rem;
  }

  .location {
    font-weight: 100;
    font-size: 2.2rem;
    color: $main-text;
  }
}

.weather-box {
  width: 200px;
  margin: 0 auto 50px;
  padding: 1rem;
  text-align: center;

  .temperature {
    font-size: 3rem;
    font-weight: 900;
    color: $highlight;
  }

  .details {
    font-size: 1.8rem;
    color: $highlight;
  }
}

.forecast-box {
  display: flex;
  justify-content: space-around;

  p {
    text-align: center;
    color: $main-text;
    font-weight: 400;

    &:first-child {
      letter-spacing: 1px;
      text-transform: uppercase;
      margin-bottom: 10px;
    }
  }
}
</style>
