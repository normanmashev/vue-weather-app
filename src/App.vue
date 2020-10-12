<template>
  <div
    id="app"
    :class="
      typeof weather.main !== 'undefined' && weather.main.temp > 14
        ? 'warm-bg'
        : ''
    "
  >
    <main>
      <div class="search-container">
        <input
          type="text"
          class="search__input"
          placeholder="Search..."
          v-model="query"
          @keypress="fetchWeather"
        />
      </div>
      <div class="weather-container" v-if="typeof weather.main != 'undefined'">
        <div class="weather__location">
          <div class="location">
            {{ weather.name }}, {{ weather.sys.country }}
          </div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>
        <div class="weather__temp">
          <div class="temp">{{ Math.round(weather.main.temp) }}˚C</div>
          <div class="weather">
            {{ weather.weather[0].main }}
            <i class="fas" :class="weatherType"></i>
          </div>
          <div class="temp-info">
            <div class="temp__item">
              Feels like
              <span>{{ Math.round(weather.main.feels_like) }}˚C</span>
            </div>
            <div class="temp__item">
              Min. temp <span>{{ Math.round(weather.main.temp_min) }}˚C</span>
            </div>
            <div class="temp__item">
              Max. temp <span>{{ Math.round(weather.main.temp_max) }}˚C</span>
            </div>
            <div class="temp__item">
              Humidity <span>{{ Math.round(weather.main.humidity) }}˚</span>
            </div>
            <div class="temp__item">
              Pressure
              <span>{{ Math.round(weather.main.pressure) / 1000 }} atm</span>
            </div>
            <div class="temp__item">
              Wind <span>{{ Math.round(weather.wind.speed) }} mph</span>
            </div>
          </div>
        </div>
      </div>
      <Loader v-else />
    </main>
  </div>
</template>

<script>
import Loader from "@/components/Loader";

export default {
  name: "App",
  data() {
    return {
      api_key: process.env.VUE_APP_API_KEY,
      url_base: "http://api.openweathermap.org/data/2.5/",
      query: "London",
      weather: {},
      weatherType: "",
      loading: true,
    };
  },
  methods: {
    async fetchWeather(e) {
      if (this.loading === true || e.key == "Enter") {
        await fetch(
          `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
        )
          .then((res) => res.json())
          .then((res) => (this.weather = res))
          .catch((err) => console.log(err));
        // fa-cloud-rain fa-snowflake fa-cloud-sun fa-sun fa-cloud fa-smog
        let weather = this.weather.weather[0].main;

        if (weather === "Clouds") this.weatherType = "fa-cloud";
        else if (weather === "Rain") this.weatherType = "fa-cloud-rain";
        else if (weather === "Snow") this.weatherType = "fa-snowflake";
        else if (weather === "Sun") this.weatherType = "fa-sun";
        else if (weather === "Clear") this.weatherType = "fa-sun";
        else if (weather === "Haze") this.weatherType = "fa-smog";
      }
    },
    dateBuilder() {
      let d = new Date();
      const days = [
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
        "Sunday",
      ];
      const months = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December",
      ];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    },
  },
  mounted() {
    this.fetchWeather();
    this.loading = false;
  },
  components: {
    Loader,
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Montserrat:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,100;1,200;1,300;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  font-family: "Montserrat", Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;

  background-image: url("./assets/cold-bg.jpg");
  background-size: cover;
  background-position: bottom;
  transition: all 0.4s ease;
}

#app.warm-bg {
  background-image: url("./assets/warm-bg.jpg");
}

main {
  min-height: 100vh;
  padding: 1.5rem;

  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
}

.search-container {
  width: 100%;
  margin-bottom: 30px;
}

.search__input {
  display: block;
  width: 100%;
  padding: 15px;

  color: #313131;
  font-size: 1rem;

  border: none;
  appearance: none;
  outline: none;
  background: none;

  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0 16px 0 16px;
  box-shadow: 0 0 16px rgba(0, 0, 0, 0.25);
  transition: all 0.4s ease;
}

.search__input:focus {
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0 16px 0;
  box-shadow: 0 0 16px rgba(0, 0, 0, 0.5);
}

.location {
  color: #fff;
  font-size: 2rem;
  font-weight: bolder;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.5);
}
.date {
  color: #fff;
  font-size: 1.2rem;
  font-weight: 300;
  font-style: italic;
  text-align: center;
  margin-bottom: 30px;
}

.temp {
  display: inline-block;
  padding: 10px 30px;
  margin-bottom: 30px;

  color: #fff;
  font-size: 4rem;
  font-weight: bold;
  text-align: center;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.5);

  background: rgba(255, 255, 255, 0.25);
  border-radius: 10px;
  box-shadow: 3px 6px 9px rgba(0, 0, 0, 0.25);
}
.temp-info {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-row-gap: 30px;
}
.temp__item {
  display: flex;
  flex-direction: column;
  align-items: center;
  color: #fff;
  font-size: 2.3rem;
  font-style: italic;
  font-weight: bold;
  text-align: center;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.5);
}
.weather {
  color: #fff;
  font-size: 3rem;
  font-style: italic;
  font-weight: bold;
  text-align: center;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.5);
  margin-bottom: 3rem;
}
.fas {
  animation: 3s linear 1s infinite alternate slide;
}

@keyframes slide {
  0% {
    transform: translate(-7px, -5px);
  }
  30% {
    transform: translateX(7px, 5px);
  }
  60% {
    transform: translateX(-8px, -5px);
  }
  100% {
    transform: translateX(8px, 5px);
  }
}

@media (max-width: 768px) {
  .temp-info {
    grid-template-columns: auto !important;
  }
}

@media (max-width: 1200px) {
  .temp-info {
    grid-template-columns: repeat(2, 1fr);
  }
}
</style>
