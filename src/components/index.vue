<template>
  <div
    id="app"
    :class="
      typeof weather.main != 'undefined' && weather.main.temp > 16 ? 'warm' : ''
    "
  >
    <main>
      <br />
      <div class="date" v-show="!vi">
        Enter your city and press enter to check the weather
      </div>
      <button v-on:click="vi = !vi" v-show="!vi">Change language VI/EN</button>
      <div class="date" v-show="vi">
        Gõ thành phố và bấm enter để kiểm tra thời tiết
      </div>
      <button v-on:click="vi = !vi" v-show="vi">Đổi ngôn ngữ VI/EN</button>
      <br />
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Search..."
          v-model="city"
          @keypress="getResult"
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
          <div class="temp">{{ Math.round(weather.main.temp) }}°c</div>
          <div class="weather">{{ weather.weather[0].description }}</div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
const axios = require("axios");
export default {
  name: "app",
  data: function() {
    return {
      api_key: process.env.VUE_APP_OPENWEATHERAPI,
      url_base: "https://api.openweathermap.org/data/2.5/weather",
      city: "",
      weather: {},
      lang: "",
      vi: false,
    };
  },
  methods: {
    getResult(e) {
      if (e.key == "Enter") {
        if (this.vi) {
          this.lang = "vi";
        } else this.lang = "en";
        axios
          .get(this.url_base, {
            params: {
              appid: this.api_key,
              q: this.city,
              lang: this.lang,
              units: "metric",
            },
          })
          .then((response) => {
            // handle success
            console.log(response);
            this.setResults(response.data);
          })
          .catch(function(error) {
            // handle error
            console.log(error);
          });
      }
    },
    setResults(results) {
      this.weather = results;
      console.log(results);
    },
    dateBuilder() {
      let d = new Date();
      var months = [
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
      var days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
      ];
      if (this.vi) {
        months = [
          "Tháng 1",
          "Tháng 2",
          "Tháng 3",
          "Tháng 4",
          "Tháng 5",
          "Tháng 6",
          "Tháng 7",
          "Tháng 8",
          "Tháng 9",
          "Tháng 10",
          "Tháng 11",
          "Tháng 12",
        ];
        days = [
          "Chủ nhật",
          "Thứ 2",
          "Thứ 3",
          "Thứ 4",
          "Thứ 5",
          "Thứ 6",
          "Thứ 7",
        ];
      }
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
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: "montserrat", sans-serif;
}

#app {
  background-image: url("../assets/images/cold-bg.jpg");
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

#app.warm {
  background-image: url("../assets/images/warm-bg.jpg");
}

main {
  min-height: 100vh;
  padding: 25px;

  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;

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
  color: #fff;
  font-size: 50px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.date {
  color: lightgray;
  padding-top: 10px;
  font-size: 25px;
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
  color: #fff;
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: lightgray;
  text-transform: capitalize;
  font-size: 40px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
button {
  width: 60%;
  padding: 5px;
  font-size: 3.5vw;
  display: flex;
  justify-content: center;
  opacity: 0.6;
  margin: 0 20% 0;
}
@media screen and (min-width: 780px) {
  .search-box {
    display: flex;
    width: 50%;
    margin: 0 25% 5%;
  }
  button {
    width: 20%;
    font-size: 20px;
    margin: 0 40% 0;
  }
}
</style>
