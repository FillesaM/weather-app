<script setup>
import axios from 'axios'
import { useRouter } from 'vue-router';
import {ref} from 'vue'
import CityList from '../components/CityList.vue';
import CityCardsSceleton from '../components/CityCardsSceleton.vue';

const mapboxApiKey = "pk.eyJ1IjoiZm1laGFqIiwiYSI6ImNsOXd4dGhsZDA2ZWszeG5vdGpwODAwbzYifQ.MN-nH3HpwjBMZU4e0VnBcg"
const searchQuery = ref('')
const queryTimeout = ref(null)
const mapboxSearchResults = ref(null)
const searchError = ref(null)
const getSearchResults= () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async () =>{
    if(searchQuery.value !== '') {
      try{
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxApiKey}&types=place`);
      mapboxSearchResults.value = result.data.features;
      }catch(error){
        searchError.value = true
      }
      return;
    }
    mapboxSearchResults.value = null
  },300)
}
const router = useRouter();
const previewCity=(searchResult)=>{
  console.log(searchResult)
  const [city,state] = searchResult.place_name.split(",")
  router.push({
    name:'cityView',
    params:{state:state.replaceAll(" ", ""), city:city.replaceAll(" ", "")},
    query:{
      lat:searchResult.geometry.coordinates[1],
      lng:searchResult.geometry.coordinates[0],
      preview: true
    }
  })
}

</script>

<template>
<main class="container text-white">
  <div class="pt-4 mb-8 relative">
    <input placeholder="Search for a city or state" 
    class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
    v-model="searchQuery" @input="getSearchResults"/>
    <ul v-if="mapboxSearchResults" class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]">
      <p v-if="searchError">Sorry, something went wrong, please try again</p>
      <p v-if="!searchError && mapboxSearchResults.length === 0">No results match your query, try a different term</p>
      <template v-else>
      <li v-for="searchResult in mapboxSearchResults" :key="searchResult.id" @click="previewCity(searchResult)"> 
       {{searchResult.place_name}}
      </li>
    </template>
    </ul>
  </div>
  <div class="flex flex-col gap-4">
    <Suspense>
      <CityList/>
      <template #fallback>
        <CityCardsSceleton />
      </template>
    </Suspense>
  </div>
</main>
</template>
