<template>
  <div id="app">
    <main>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Type a city..."
          v-model="query"
          @keypress="fetchWeather"
        />
      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">
            {{ weather.name }}, {{ weather.sys.country }}
          </div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div v-bind:class="tempS" id="temp">
            {{ Math.round(weather.main.temp) }} C
          </div>
          <div class="weather">{{ weather.weather[0].main }}</div>
        </div>
      </div>

      <footer></footer>
    </main>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      api_key: "620cb8cc343201300b251cb1dcb52d5e",
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weather: {},
      tempS: "temp",
    };
  },

  methods: {
    fetchWeather(e) {
      if (e.key == "Enter") {
        fetch(
          `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
        )
          .then((res) => {
            return res.json();
          })
          .then(this.setResults);
      }
    },
    showTemp() {
      let temp = this.weather.main.temp;
      if (temp > 5 && temp < 30) {
        this.tempS = "temp warm";
      } else if (temp > -21 && temp <= 5) {
        this.tempS = "temp cold";
      } else if (temp >= 30) {
        this.tempS = "temp hot";
      } else if (temp <= -21) {
        this.tempS = "temp vcold";
      }
      return temp;
    },
    setResults(results) {
      this.weather = results;
      this.showTemp();
    },

    dateBuilder() {
      let d = new Date();
      let months = [
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
      let days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
      ];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day}, ${date} ${month} ${year}`;
    },
  },
};
</script>

<style>
body {
  font-family: "Lato", Arial, sans-serif;
  margin: 0;
  padding: 100px 150px;
  background-color: lightgray;
}

#app {
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
  border-radius: 15px;
  margin: 10px 0px;
  padding: 20px;
  background-color: whitesmoke;
}

.location-box .location {
  font-size: 32px;
  font-weight: 500;
  text-align: center;
}

.location-box .date {
  font-size: 20px;
  font-weight: 300;
  text-align: center;
}

.weather-box {
  text-align: center;
  background-color: rgba(255, 255, 255, 0.25);
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  font-size: 102px;
  font-weight: 900;

  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.hot {
  background-color: red;
}

.warm {
  background-color: bisque;
}

.cold {
  background-color: cyan;
}

.vcold {
  background-color: cornflowerblue;
}

.weather-box .weather {
  font-size: 48px;
  font-weight: 700;
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  box-sizing: border-box;

  color: #313131;
  font-size: 20px;

  appearance: none;
  border: none;
  outline: none;
  background: none;

  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 10px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
}
</style>
