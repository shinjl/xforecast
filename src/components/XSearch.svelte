<script lang="ts">
    import { onMount } from 'svelte';
    import { createEventDispatcher } from 'svelte';

    let dispatch = createEventDispatcher();

    export let searchUrl = '';
    export let searchText = '';

    let searchInput: any;

    /**
     * Fetch cities from the server
     * @param name City name for search
     */
    const fetchData = async (name: string) => {
        try {
            const response = await fetch(`${searchUrl}/${name}`);
            return await response.json();
        } catch (error) {
            console.log(error);
        }
        return {};
    };

    /**
     * Initialize auto complete for searching cities
     */
    const initialize = () => {
        autocomplete({
            input: searchInput,
            emptyMsg: 'No matching items found',
            minLength: 2,
            fetch: async (text: any, update: any) => {
                text = text.toLowerCase();
                const data = await fetchData(text);
                update(data.cities);
            },
            onSelect: (item: any) => {
                searchInput.value = item.label;
                dispatch('searchChange', { lat: item.lat, lon: item.lon, city: searchInput.value });
            },
        });
    };

    onMount(initialize);
</script>

<div class="search">
    <input
        bind:this={searchInput}
        bind:value={searchText}
        type="text"
        placeholder="Type here to search your location or click on the map"
        maxlength="256"
    />
</div>

<style>
    .search {
        margin-bottom: 0.3rem;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    .search input {
        outline: none;
        border: 1px solid #dddddd;
        color: #555555;
        background: #fafafa;
        flex: 1;
        font-size: 1rem;
        background-image: url('/images/search.svg');
        background-size: 24px 24px;
        background-repeat: no-repeat;
        background-position: 0.3rem center;
        padding: 0.5rem 0.5rem 0.5rem 2.5rem;
        border-radius: 3px;
    }

    .search input:focus {
        border-color: #f65751;
        background-image: url('/images/search-on.svg');
    }
</style>
