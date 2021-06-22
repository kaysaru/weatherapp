<template>
  <v-app>
    <v-app-bar fixed dark>
      <v-app-bar-nav-icon @click="drawer = true"></v-app-bar-nav-icon>
      <v-toolbar-title>Weather App</v-toolbar-title>
      <v-toolbar flat floating>
        <v-text-field
          hide-details
          prepend-icon="mdi-magnify"
          single-line
          v-model="query"
          @keypress="fetchWeather"
          label="Type a city"
          clearable
          hint="For example, type 'Nur-Sultan, KZ'"
        ></v-text-field>

        <v-btn icon>
          <v-icon>mdi-crosshairs-gps</v-icon>
        </v-btn>
      </v-toolbar>
    </v-app-bar>

    <v-navigation-drawer v-model="drawer" temporary fixed>
      <v-list-item>
        <v-list-item-content>
          <v-list-item-title class="text-h6">City Menu</v-list-item-title>
          <v-list-item-subtitle>
            Bookmark cities for fast access
          </v-list-item-subtitle>
        </v-list-item-content>
      </v-list-item>
      <v-list-item>
        <v-list-item-content>
          <v-text-field
            v-model="city1"
            label="Type a city"
            clearable
            hint="For example, type 'Nur-Sultan, KZ'"
          ></v-text-field>

          <v-btn small elevation="" color="" @click="addCity(city1)">ADD</v-btn>
        </v-list-item-content>
      </v-list-item>

      <v-divider></v-divider>

      <v-list dense>
        <v-subheader>Chosen cities</v-subheader>
        <v-list-item-group v-model="selectedCity" color="primary">
          <v-list-item v-for="city in bookmarked" @click="findL(city)">
            <v-list-item-icon>
              <v-icon style="margin-top: 20px">mdi-city</v-icon>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title v-text="city"></v-list-item-title>
            </v-list-item-content>
            <v-list-item-action>
              <v-btn icon @click="deleteEl(city)">
                <v-icon color="grey lighten-1">mdi-delete</v-icon>
              </v-btn>
            </v-list-item-action>
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </v-navigation-drawer>

    <br /><br /><br /><br /><br /><br />

    <v-main>
      <v-container
        class=""
        v-if="query.length < 1 && typeof weather.main == 'undefined'"
      >
        <v-row>
          <v-col class="text-h1" align="center">Weather App PWA</v-col>
        </v-row>
        <v-row>
          <v-col class="text-h3" align="center">
            Type a city above or choose one of your favourite cities
          </v-col>
        </v-row>
      </v-container>
      <v-container class="" v-if="typeof weather.main != 'undefined'">
        <v-row max-height="400px">
          <v-col>
            <v-card class="" elevation="" max-width="">
              <v-list-item two-line>
                <v-list-item-content>
                  <v-list-item-title class="text-h5">
                    {{ weather.name }}, {{ weather.sys.country }}
                  </v-list-item-title>
                  <v-list-item-subtitle>
                    {{ dateBuilder() }}
                  </v-list-item-subtitle>
                </v-list-item-content>
              </v-list-item>
              <v-card-text>
                <v-row align="center">
                  <v-col class="text-h2" cols="3">
                    {{ Math.round(weather.main.temp) }}&deg;C
                  </v-col>
                  <v-col cols="2">
                    <v-icon x-large v-bind:color="iconColor">{{setIcon()}} </v-icon>
                  </v-col>
                  <v-col>
                    {{ weather.weather[0].main }} <br />
                    Feels like {{ Math.round(weather.main.feels_like) }} &deg;C
                  </v-col>
                </v-row>
                <v-row>
                  <v-col>
                    <v-icon>mdi-weather-windy</v-icon>
                    >{{ weather.wind.speed }} m/s, {{ weather.wind.deg }}&deg;
                  </v-col>

                  <v-col>
                    <v-icon>mdi-water-percent</v-icon>
                    {{ weather.main.humidity }}%
                  </v-col>
                </v-row>
                <v-row>
                  <v-col>
                    <v-icon>mdi-weight</v-icon>
                    {{ weather.main.pressure }} hPa
                  </v-col>
                  <v-col>
                    <v-icon>mdi-eye</v-icon>
                    {{ weather.visibility }} m
                  </v-col>
                </v-row>
              </v-card-text>

              <v-divider class=""></v-divider>
            </v-card>
          </v-col>
        </v-row>

        <v-divider class="" style="margin: 20px 0px 20px 0px"></v-divider>

        <v-row>
          <v-col>
            <v-card class="" elevation="" max-width="">
              <v-card-text>
                <p class="">Weather in other cities</p>
                <div class="text--primary"></div>
              </v-card-text>
              <v-card-actions>
                <v-btn text color=""> </v-btn>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      drawer: false,
      selectedCity: 0,
      bookmarked: [],
      city1: "",
      api_key: "a4fa6759c54d8ee8a070d5250b21cad2",
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weather: {},
      iconColor: ""
    };
  },

  methods: {
    deleteEl(el) {
      for (let i = 0; i < this.bookmarked.length; ++i) {
        if (el === this.bookmarked[i]) {
          this.bookmarked.splice(i, 1);
        }
      }
    },
    findL(city) {
      this.query = city;
      fetch(
        `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
      )
        .then((res) => {
          return res.json();
        })
        .then(this.setResults);
    },
    addCity() {
      this.bookmarked.push(this.city1);
      this.city1 = "";
    },
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
    setResults(results) {
      this.weather = results;
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
    setIcon() {
      /*
      var t = new Date();
      var a = Date.now() - 6 * 60 * 60 * 1000;
      t.setTime(a - this.weather.timezone * 1000);*/
      
      if(this.weather.weather[0].icon[this.weather.weather[0].icon.length - 1] == 'd') {
        this.iconColor = "yellow darken-1";
      }
      else {
        this.iconColor = "blue darten-2";
      }

      var text = "";
      if(this.weather.weather[0].icon == "01d") {
        text = "weather-sunny";
      }
      else if(this.weather.weather[0].icon == "02d") {
        text = "weather-partly-cloudy";
      }
      else if(this.weather.weather[0].icon == "03d") {
        text = "weather-cloudy";
      }
      else if(this.weather.weather[0].icon == "04d") {
        text = "weather-cloudy";
      }
      else if(this.weather.weather[0].icon == "09d") {
        text = "weather-pouring";
      }
      else if(this.weather.weather[0].icon == "10d") {
        text = "weather-rainy";
      }
      else if(this.weather.weather[0].icon == "11d") {
        text = "weather-lightning";
      }
      else if(this.weather.weather[0].icon == "13d") {
        text = "weather-snowy";
      }
      else if(this.weather.weather[0].icon == "50d") {
        text = "weather-fog";
      }
      else if(this.weather.weather[0].icon == "01n") {
        text = "moon-waning-crescent";
      }
      else if(this.weather.weather[0].icon == "02n") {
        text = "weather-partly-cloudy";
      }
      else if(this.weather.weather[0].icon == "03n") {
        text = "weather-cloudy";
      }
      else if(this.weather.weather[0].icon == "04n") {
        text = "weather-cloudy";
      }
      else if(this.weather.weather[0].icon == "09n") {
        text = "weather-pouring";
      }
      else if(this.weather.weather[0].icon == "10n") {
        text = "weather-rainy";
      }
      else if(this.weather.weather[0].icon == "11n") {
        text = "weather-lightning";
      }
      else if(this.weather.weather[0].icon == "13n") {
        text = "weather-snowy";
      }
      else if(this.weather.weather[0].icon == "50n") {
        text = "weather-fog";
      }
      
      return "mdi-" + text;
    }
  }
};
</script>