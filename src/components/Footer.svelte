<footer>
    <div class="footer-logo">
        <img src="/images/logo.png" alt="Footer Logo" />
    </div>
    <div class="footer-info">
        xForecast - A Simple Weather Forecast - <a href="https://shinjl.com" target="_blank">SHINJL.COM</a>
    </div>
    <div class="links">
        Powered by <a href="https://svelte.dev" target="_blank">Svelte</a>,
        <a href="https://highcharts.com" target="_blank">Highcharts</a>,
        <a href="https://ipstack.com" target="_blank">IPStack</a>,
        <a href="https://openweathermap.org" target="_blank">OpenWeather</a>,
        <a href="https://leafletjs.com" target="_blank">LeafLet</a> and
        <a href="https://www.openstreetmap.org" target="_blank">OpenStreetMap</a>
    </div>
</footer>

<style>
    footer {
        text-align: center;
        margin: 2rem auto;
        font-size: 0.8rem;
    }

    .footer-logo img {
        height: 48px;
    }

    .footer-info {
        color: #aaaaaa;
    }

    .links {
        color: #aaaaaa;
        padding: 0.8rem 0 0;
    }

    a {
        color: #999999;
        text-decoration: none;
    }

    a:hover {
        color: #f65751;
    }
</style>
