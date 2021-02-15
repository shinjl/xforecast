<script lang="ts">
    import { onMount } from 'svelte';
    import XSearch from '$components/XSearch.svelte';
    import XChart from '$components/XChart.svelte';
    import XMap from '$components/XMap.svelte';
    import Header from '$components/Header.svelte';
    import Footer from '$components/Footer.svelte';

    const baseUrl = 'https://xforecast-server.herokuapp.com';

    let loading = true;
    let currentWeather: any = {};
    let dailyForecast = [];
    let hourlyForecast = [];
    let searchText = '';
    let myLocation = {
        lat: 35.689499,
        lon: 139.691711,
    };

    /**
     * Initialize: shows weather based on user location (GeoIP)
     */
    const initialize = async () => {
        loading = true;
        const data: any = await fetchData();
        await convertData(data);
        loading = false;
    };

    /**
     * Convert data for client
     * @param data Data from the server
     */
    const convertData = async (data: any) => {
        if (!data) {
            return;
        }

        const {
            forecast: { current, daily, hourly },
        } = data;

        const convertDisplayText = (value: any, unit: string) => {
            if (value == undefined || value == null) {
                return '&mdash;';
            }
            return `${value}${unit}`;
        };
        currentWeather = {
            temp: convertDisplayText(current.temp, '&deg;'),
            feels_like: convertDisplayText(current.feels_like, '&deg;'),
            pressure: convertDisplayText(current.pressure, 'hPa'),
            humidity: convertDisplayText(current.humidity, '%'),
            uvi: convertDisplayText(current.uvi, ''),
            clouds: convertDisplayText(current.clouds, '%'),
            visibility: convertDisplayText(current.visibility, ''),
            wind_speed: convertDisplayText(current.wind_speed, 'm/s'),
            wind_deg: convertDisplayText(current.wind_deg, '&deg;'),
            rain: convertDisplayText(current.rain ? current.rain['1h'] : null, 'mm'),
            snow: convertDisplayText(current.snow ? current.snow['1h'] : null, 'mm'),
            icon: current.weather[0].icon,
        };

        dailyForecast = [];
        daily.forEach((one: any) => {
            let date = new Date(one.dt * 1000);
            date.setHours(0);
            date.setMinutes(0);
            date.setSeconds(0);
            date.setMilliseconds(0);
            dailyForecast.push({
                x: date.getTime(),
                y: Math.floor(one.temp.day),
                icon: `url(https://openweathermap.org/img/wn/${one.weather[0].icon}@2x.png)`,
            });
        });

        let counter = 0;
        hourlyForecast = [];
        for (const one of hourly) {
            let date = new Date(one.dt * 1000);
            date.setMinutes(0);
            date.setSeconds(0);
            date.setMilliseconds(0);
            hourlyForecast.push({
                x: date.getTime(),
                y: Math.floor(one.temp),
                icon: null,
            });
            counter++;
            if (counter > 24) {
                break;
            }
        }
    };

    /**
     * Fetch data from server
     * @param url fetch URL
     */
    const fetchData = async (url: string = baseUrl) => {
        try {
            const response = await fetch(url);
            return await response.json();
        } catch (error) {
            console.log(error);
        }
        return null;
    };

    /**
     * Get weather data from the selected location
     */
    const locationChange = async ({ detail: { lat, lon } }) => {
        loading = true;
        const data = await fetchData(`${baseUrl}/location/?lat=${lat}&lon=${lon}`);
        convertData(data);
        searchText = '';
        loading = false;
    };

    /**
     * Get weather data from the selected city
     */
    const searchChange = async ({ detail: { lat, lon } }) => {
        loading = true;
        const data = await fetchData(`${baseUrl}/location/?lat=${lat}&lon=${lon}`);
        convertData(data);

        myLocation.lat = lat;
        myLocation.lon = lon;
        loading = false;
    };

    onMount(initialize);
</script>

<Header />
<main class="main">
    <div class="info">
        <div class="current">
            <div class="current-weather">
                <h2>Current Weather</h2>
                <div class="icon-container">
                    {#if loading || !currentWeather.icon}
                        <img class="loading-icon" src="/images/loading.svg" alt="Loading" />
                    {:else}
                        <img
                            class="current-weather-icon"
                            src="https://openweathermap.org/img/wn/{currentWeather.icon}@2x.png"
                            alt=""
                        />
                    {/if}
                </div>
            </div>

            <div><img class="weather-icon" src="/images/weather-temperature.svg" alt="temperature icon" /></div>
            <div class="label">Temperature</div>
            <div class="text" contenteditable="true" bind:innerHTML={currentWeather.temp} />

            <div class="left-padding">
                <img class="weather-icon" src="/images/weather-feels-like.svg" alt="feels like icon" />
            </div>
            <div class="label">Feels Like</div>
            <div class="text" contenteditable="true" bind:innerHTML={currentWeather.feels_like} />

            <div><img class="weather-icon" src="/images/weather-pressure.svg" alt="pressure icon" /></div>
            <div class="label">Pressure</div>
            <div class="text" contenteditable="true" bind:innerHTML={currentWeather.pressure} />

            <div class="left-padding">
                <img class="weather-icon" src="/images/weather-humidity.svg" alt="humidity icon" />
            </div>
            <div class="label">Humidity</div>
            <div class="text" contenteditable="true" bind:innerHTML={currentWeather.humidity} />

            <div><img class="weather-icon" src="/images/weather-uv.svg" alt="UV icon" /></div>
            <div class="label">UV Index</div>
            <div class="text" contenteditable="true" bind:innerHTML={currentWeather.uvi} />

            <div class="left-padding">
                <img class="weather-icon" src="/images/weather-clouds.svg" alt="clouds icon" />
            </div>
            <div class="label">Cloud</div>
            <div class="text" contenteditable="true" bind:innerHTML={currentWeather.clouds} />

            <div><img class="weather-icon" src="/images/weather-wind.svg" alt="wind icon" /></div>
            <div class="label">Wind Speed</div>
            <div class="text" contenteditable="true" bind:innerHTML={currentWeather.wind_speed} />

            <div class="left-padding"><img class="weather-icon" src="/images/weather-wind.svg" alt="wind icon" /></div>
            <div class="label">Wind Dir</div>
            <div class="text" contenteditable="true" bind:innerHTML={currentWeather.wind_deg} />

            <div><img class="weather-icon" src="/images/weather-rain.svg" alt="rain icon" /></div>
            <div class="label">Rain</div>
            <div class="text" contenteditable="true" bind:innerHTML={currentWeather.rain} />

            <div class="left-padding"><img class="weather-icon" src="/images/weather-snow.svg" alt="snow icon" /></div>
            <div class="label">Snow</div>
            <div class="text" contenteditable="true" bind:innerHTML={currentWeather.snow} />
        </div>
        <div class="map">
            <XSearch searchUrl={`${baseUrl}/cities`} on:searchChange={searchChange} bind:searchText />
            <XMap on:locationChange={locationChange} {myLocation} />
        </div>
    </div>
    <div class="chart">
        <XChart data={hourlyForecast} title="Hourly Forecast" timezone="Asia/Tokyo" />
    </div>
    <div class="chart">
        <XChart data={dailyForecast} title="Daily Forecast" timezone="Asia/Tokyo" tooltipFormat="%m/%d" />
    </div>
</main>
<Footer />

<style>
    .main {
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        margin: 0 auto;
        max-width: 1200px;
    }

    .info {
        margin: 0.8rem 0;
        display: flex;
        justify-content: space-between;
    }

    .current {
        flex-grow: 1;
        display: grid;
        grid-template-columns: 45px 1fr 1fr 150px 1fr 1fr;
        grid-template-rows: 100px repeat(5, 45px);
        align-items: center;
        margin: 0 3rem 0 1rem;
    }

    .current-weather {
        grid-column-start: 1;
        grid-column-end: 7;
        display: flex;
        justify-content: center;
        align-items: center;
        margin-bottom: 2rem;
    }

    .current-weather h2 {
        color: #2b2b2b;
        margin-right: 50px;
    }

    .icon-container {
        width: 80px;
        height: 80px;
    }

    .current-weather-icon {
        background: #ffffff;
        width: 80px;
        height: 80px;
        border-radius: 50%;
        border: 1px solid #dddddd;
    }

    .loading-icon {
        margin: 25px;
        width: 32px;
        height: 32px;
        animation: rotation 2s infinite linear;
    }

    @keyframes rotation {
        from {
            transform: rotate(0deg);
        }
        to {
            transform: rotate(359deg);
        }
    }

    .weather-icon {
        width: 24px;
        height: 24px;
        opacity: 0.6;
    }

    .left-padding {
        margin-left: 105px;
    }

    .label {
        text-align: left;
        color: #2b2b2b;
        font-size: 1rem;
    }

    .text {
        text-align: right;
        color: #000000;
        font-size: 1.2rem;
        font-style: italic;
    }

    .map {
        display: flex;
        flex-direction: column;
        justify-content: space-between;
    }

    .chart {
        margin: 0.8rem 0;
    }
</style>
