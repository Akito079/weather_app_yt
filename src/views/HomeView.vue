<template>
  <div>
    <main class="container text-white">
      <div class="pt-4 mb-8 relative">
        <input
          type="text"
          @input="getSearchResults()"
          class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
          placeholder="Search for a City or State"
          v-model="searchKey"
          id=""
        />
        <ul
          v-if="mapboxSearchResults"
          class="absolute top-[66px] bg-weather-secondary text-white w-full shadow-md py-2 px-1"
        >
          <p v-if="searchErors">Sorry, something went wrong.Please try again</p>
          <p v-if="!searchErors && mapboxSearchResults.length === 0">
            No result match your query,try a different term.....
          </p>
          <template v-else>
            <li
              v-for="searchResult in mapboxSearchResults"
              :key="searchResult.id"
              class="py-2 cursor-pointer"
              @click="previewCity(searchResult)"
            >
              {{ searchResult.place_name }}
            </li>
          </template>
        </ul>
      </div>
      <div class="flex flex-col gap-4">
        <Suspense>
         <CityList/>
          <template #fallback>
            <div>
            <CityCardSkeleton/>
            </div>
          </template>
        </Suspense>
      </div>
    </main>
  </div>
</template>

<script>
import axios from "axios";
import {useRouter} from 'vue-router'
import CityList from "../components/CityList.vue";
import CityCardSkeleton from "../components/CityCardSkeleton.vue";
export default {
    data() {
        return {
            searchKey: "",
            queryTimeout: null,
            mapboxApiKey: `pk.eyJ1IjoiYWtpdG8wNzkiLCJhIjoiY2xwazBzbHk4MDVmazJpcXYycWt0OWg2YyJ9.Nf7R4bOeR0Tk0yhb1uRZYg`,
            mapboxSearchResults: null,
            searchErors: null,
            router: useRouter(),
        };
    },
    methods: {
        getSearchResults() {
            clearTimeout(this.queryTimeout);
            this.queryTimeout = setTimeout(async () => {
                if (this.searchKey !== "") {
                    try {
                        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${this.searchKey}.json?access_token=${this.mapboxApiKey}&types=place`);
                        this.mapboxSearchResults = result.data.features;
                    }
                    catch {
                        this.searchErors = true;
                    }
                    return;
                }
                this.mapboxSearchResults = null;
            }, 300);
        },
        previewCity(searchResult) {
            const [city, state] = searchResult.place_name.split(',');
            this.router.push({
                name: 'cityView',
                params: { state: state.replaceAll(" ", ""), city: city },
                query: {
                    lat: searchResult.geometry.coordinates[1],
                    lng: searchResult.geometry.coordinates[0],
                    preview: true,
                }
            });
        }
    },
    components: { CityList, CityCardSkeleton }
};
</script>
