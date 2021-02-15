<script lang="ts">
    import { onMount } from 'svelte';
    import { createEventDispatcher } from 'svelte';

    let dispatch = createEventDispatcher();

    // Default map location: Tokyo
    export let myLocation = {
        lat: 35.689499,
        lon: 139.691711,
    };
    export let defaultZoomLevel = 8;

    // Automatically updates map when lat/lon changes
    $: {
        if (xmap) {
            const latlng = {
                lat: myLocation.lat,
                lng: myLocation.lon,
            };
            gotoMap(latlng);
        }
    }

    let xmap: any;
    let marker: any;

    /**
     * Sets the view of the map to center the given lat/lon, and adds the marker
     * @param latlng lat/lon
     */
    const gotoMap = (latlng: any) => {
        var sunIcon = L.icon({
            iconUrl: '/images/location.png',
            iconSize: [56, 56],
            iconAnchor: [28, 56],
        });

        let zoom = xmap.getZoom();
        if (zoom < defaultZoomLevel) {
            zoom = defaultZoomLevel;
        }
        xmap.setView(latlng, zoom);

        if (marker) {
            xmap.removeLayer(marker);
        }
        const { lat, lng } = latlng;
        marker = L.marker([lat, lng], { icon: sunIcon }).addTo(xmap);
    };

    /**
     * Initialize the component
     */
    const initialize = () => {
        xmap = L.map('xmap').setView([myLocation.lat, myLocation.lon], defaultZoomLevel);
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
        }).addTo(xmap);

        xmap.on('click', (e: any) => {
            gotoMap(e.latlng);
            dispatch('locationChange', { lat: e.latlng.lat, lon: e.latlng.lng });
        });
    };

    onMount(initialize);
</script>

<div id="xmap" />

<style>
    #xmap {
        width: 600px;
        height: 300px;
        box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24);
        background: #ffffff;
        margin-left: 1px;
    }
</style>
