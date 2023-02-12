<template lang="">
  <main :class="this.temperature != undefined && this.temperature > 25 ? 'warm' : 'cold'">
    <div class="filter">
      <input
        class="search"
        type="text"
        placeholder="Enter location..."
        v-model="keyWord"
        @keyup.enter="search($event)" />
      <section class="demo-area">
        <div>
          <p class="location">{{ searchLocation }}</p>
          <p class="date">{{ searchDate }}</p>
        </div>
        <div class="temperature">{{ temperature }}&#176;c</div>
        <div class="weather-dp">{{ weatherDp }}</div>
        <div class="air-quality">
          <div class="pm-2_5">{{ pm25 }}</div>
          <div class="so-2">{{ so2 }}</div>
        </div>
        <div class="epa-index">EPA index: {{ epaIndex }}</div>
        <div class="epa-meaning">
          EPA index is measured on a scale of 1 to 6, larger number means worse air quality.
        </div>
      </section>
    </div>
  </main>
</template>

<script>
import axios from "axios";
export default {
  name: "Weather",
  data() {
    return {
      api_key: "185c74293df340139a412341222412",
      url_base: "https://api.weatherapi.com/v1",
      keyWord: "",
      temperature: undefined,
      weatherDp: "",
      searchLocation: "",
      searchDate: "",
      pm25: "",
      so2: "",
      epaIndex: "",
    };
  },
  methods: {
    search() {
      axios
        .get(`${this.url_base}/current.json`, {
          params: {
            key: `${this.api_key}`,
            q: `${this.keyWord}`,
            aqi: "yes",
          },
        })
        .then(({ data }) => {
          console.log(data);
          this.searchLocation = data.location.name + " " + data.location.country;
          let tempDate = new Date(data.location.localtime).toString();
          //string才能調用match，返回值為數組。 有g則返回所有匹配的结果，但不會返回捕獲組
          //https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/match
          tempDate = tempDate.match(/^[A-z]+\s[A-z]+\s\d{2}\s\d{4}\s\d{2}:\d{2}:\d{2}/g);
          this.searchDate = tempDate[0];
          this.temperature = data.current.temp_c;
          this.weatherDp = data.current.condition.text;
          this.pm25 = data.current.air_quality.pm2_5.toFixed(2);
          this.so2 = data.current.air_quality.so2.toFixed(2);
          this.keyWord = "";
          this.epaIndex = data.current.air_quality["us-epa-index"];
        })
        .catch((err) => {
          if (err) {
            alert(`There is no location called "${this.keyWord}". Please try again!`);
            this.keyWord = "";
          }
        });
    },
  },
  mounted() {
    this.keyWord = "Taipei";
    this.search();
  },
};
</script>

<style lang="css" scoped>
* {
  color: white;
}

main {
  transition: all 1s;
  margin: 100px auto;
  text-align: center;
  font-family: "montserrat", sans-serif;
  background-repeat: no-repeat;
  background-position: center center;
  background-size: cover;
  width: 400px;
  height: 600px;
  border-radius: 25px;
}
main.cold {
  background-image: url(@/assets/cold.jpg);
}

main.warm {
  background-image: url(@/assets/hot.jpg);
}

.filter {
  width: 100%;
  height: 100%;
  border-radius: 25px;
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.4) 70%, rgba(0, 0, 0, 0.75));
}
.search {
  width: 80%;
  font-size: 15px;
  padding: 7px;
  margin: 20px 0;
  border-radius: 0 12px 0 12px;
  border: none;
  outline: none;
  background-color: rgba(255, 255, 255, 0.7);
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.25);
  opacity: 0.8;
  transition: 0.4s;
  color: #000;
}
.search:focus {
  border-radius: 12px 0 12px 0;
  box-shadow: 0 0 16px rgba(0, 0, 0, 0.25);
  background-color: white;
}

.demo-area {
  width: 60%;
  margin: 0 auto;
}
.demo-area > div:first-child {
  margin-bottom: 20px;
}
.demo-area .location {
  font-size: 2rem;
  font-weight: 700;
}
.demo-area .date {
  font-size: 15px;
  font-style: italic;
}
.demo-area .temperature {
  font-size: 45px;
  font-weight: 900;
  /* width: 120px; */
  margin: 0 auto 20px;
  border-radius: 10px;
  background-color: rgba(255, 255, 255, 0.35);
  text-shadow: 2px 3px rgba(0, 0, 0, 0.5);
  box-shadow: 2px 3px rgba(0, 0, 0, 0.4);
  padding: 5px;
}
.demo-area .weather-dp {
  font-size: 27px;
  /* font-style: italic; */
  font-weight: 900;
  text-shadow: 2px 1px rgba(0, 0, 0, 0.7);
}

.air-quality {
  display: flex;
  justify-content: space-around;
  font-size: 30px;
  font-weight: 900;
}
.air-quality div {
  margin-top: 30px;
  border-radius: 10px;
  background-color: rgba(255, 255, 255, 0.35);
  text-shadow: 2px 3px rgba(0, 0, 0, 0.5);
  box-shadow: 2px 3px rgba(0, 0, 0, 0.4);
  padding: 5px;
  position: relative;
  margin-right: 5px;
}

.pm-2_5::after {
  content: "PM2.5 (μg/m3)";
  width: 100px;
  font-size: 15px;
  font-weight: 500;
  text-shadow: none;
  position: absolute;
  bottom: -20px;
  left: 0;
}

.so-2::after {
  content: "SO2 (ppb)";
  width: 100px;
  font-size: 15px;
  font-weight: 500;
  text-shadow: none;
  position: absolute;
  bottom: -20px;
  left: 2px;
}

.epa-index {
  border-radius: 10px;
  background-color: rgba(255, 255, 255, 0.35);
  text-shadow: 2px 3px rgba(0, 0, 0, 0.5);
  box-shadow: 2px 3px rgba(0, 0, 0, 0.4);
  padding: 5px;
  font-size: 27px;
  margin-top: 50px;
}

.epa-meaning {
  margin-top: 8px;
  font-size: 15px;
  font-weight: 500;
  text-shadow: none;
  /* text-align: start; */
  line-height: 18px;
}
</style>
