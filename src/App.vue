<template>
    <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp > 16 ? 'warm': ''">
        <main>
            <button type="button" @click="getLocation">Location</button>
            <div class="search-box">
                <input type="text" name="" value="" class="search-bar" placeholder="Search..." v-model="query" @keypress="weatherCityName">
            </div>
            <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
                <div class="location-box">
                    <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
                    <div class="date">{{ dateBuilder() }}</div>
                </div>

                <div class="weather-box">
                    <div class="temp">{{ Math.round(weather.main.temp) }}°c</div>
                    <div class="weather">{{ weather.weather[0].main}}</div>
                </div>
            </div>
            <div v-if="weather.cod != undefined && weather.cod == 404">
                <div class="location-box">
                    <div class="date">{{ weather.message }}, re-enter city name.</div>
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
            api_key: '',
            url_base: 'https://api.openweathermap.org/data/2.5',
            query: '',
            weather: {},
            lat: '',
            long: ''
        }
    },
    created() {
        if( "geolocation" in navigator ) {
            navigator.geolocation.getCurrentPosition(this.showPosition);
        }
    },
    methods: {
        weatherCityName(e) {
            if( e.key == "Enter" ) {
                let query = `q=${this.query}`;
                this.fetchWeather(query);
            }
        },
        weatherGeographicCoordinates() {
            if( this.lat != '' && this.long != '' ) {
                let query = `lat=${this.lat}&lon=${this.long}`;
                this.fetchWeather(query);
            }
        },
        fetchWeather(query = '') {
            fetch(`${this.url_base}/weather?${query}&units=metric&APPID=${this.api_key}`)
            .then(res => {
                return res.json();
            }).then(this.setResults);
        },
        setResults(results) {
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
        },
        getLocation() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(this.showPosition);
            } else {
                alert("Geolocation is not supported by this browser.");
            }
        },
        showPosition(position) {
            this.lat  = position.coords.latitude;
            this.long = position.coords.longitude;
            this.weatherGeographicCoordinates();
        }
    }
}
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
    background-image: url('./assets/cold-bg.jpg');
    background-size: cover;
    background-position: bottom;
    transition: .4s;
}

#app.warm {
    background-image: url('./assets/warm-bg.jpg');
}

main {
    min-height: 100vh;
    padding: 25px;
    background-image: linear-gradient(to bottom, rgba(0,0,0,.25), rgba(0,0,0,.75));
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
    box-shadow: 0 0 8px rgba(0,0,0,.25);
    background-color: rgba(255,255,255,.5);
    border-radius: 0 16px;
    transition: .4s;
}

.search-box .search-bar:focus {
    box-shadow: 0 0 16px rgba(0,0,0,.25);
    background-color: rgba(255,255,255,.75);
    border-radius: 16px 0;
}

.location-box .location {
    color: #fff;
    font-size: 32px;
    font-weight: 500;
    text-align: center;
    text-shadow: 1px 3px rgba(0,0,0,.25);
}

.location-box .date {
    color: #fff;
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
    color: #fff;
    font-size: 102px;
    font-weight: 900;
    text-shadow: 3px 6px rgba(0,0,0,.25);
    background-color: rgba(255,255,255,.25);
    border-radius: 16px;
    margin: 30px 0;
    box-shadow: 3px 6px rgba(0,0,0,.25);
}

.weather-box .weather {
    color: #fff;
    font-size: 48px;
    font-weight: 700;
    font-style: italic;
    text-shadow: 3px 6px rgba(0,0,0,.25);
}
</style>
