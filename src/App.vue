<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp > 16 ? 'warm' : ''">
    <main>
      <div class="search-box">
        <span class="icon-search"><i class="fas fa-search"></i></span>
        <input 
          type="text" 
          class="search-bar" 
          placeholder="Enter City or Location..."
          v-model="query"
          @keypress="fetchWeather"
        />
      </div>

      <div v-if="error" class="error-notification">
        <span class="icon-danger">⚠️</span> {{ error }}
      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">
            <span class="icon-location">📍</span> {{ weather.name }}, {{ weather.sys.country }}
          </div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{ Math.round(weather.main.temp) }}°c</div>
          <div class="weather">{{ weather.weather[0].main }}</div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: 'app',
  data() {
    return {
      api_key: '8beed40df2a396aa248799b8c3b16f40',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {},
      error: null,
    };
  },
  methods: {
    fetchWeather(e) {
      if (e.key === "Enter") {
        this.error = null;
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
          .then((res) => {
            if (!res.ok) {
              throw new Error('Location not found. Please try another place.');
            }
            return res.json();
          })
          .then(this.setResults)
          .catch((err) => {
            this.weather = {};
            this.error = err.message;
          });
      }
    },
    setResults(results) {
      this.weather = results;
    },
    dateBuilder() {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  font-family: 'montserrat', sans-serif;
}
#app {
  background-image: url('./components/cold-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}
#app.warm {
  background-image: url('./components/warm-bg.jpg');
}
main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));
}
.search-box {
  width: 100%;
  margin-bottom: 30px;
  position: relative;
}
.search-box .icon-search {
  position: absolute;
  top: 50%;
  left: 15px;
  transform: translateY(-50%);
  font-size: 18px;
  color: #313131;
}
.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px 15px 15px 40px;
  color: #313131;
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
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
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
.location-box .location .icon-location {
  font-size: 24px;
  margin-right: 8px;
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
  color: #FFF;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.error-notification {
  color: red;
  font-size: 18px;
  margin-bottom: 15px;
  text-align: center;
  display: flex;
  align-items: center;
  justify-content: center;
}
.error-notification .icon-danger {
  font-size: 20px;
  margin-right: 8px;
}
</style>