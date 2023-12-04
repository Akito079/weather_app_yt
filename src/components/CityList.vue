<template>
  <div v-for="city in savedCities" :key="city.id">
    <CityCard :city="city" @click="gotoCityView(city)"/>
  </div>
  <p v-if="savedCities.length === 0">
  No location added.To start tracking a location, search in the field above.</p>
</template>

<script setup>
import { ref } from 'vue';
import axios from  "axios";
import {useRouter} from "vue-router";
import CityCard from './CityCard.vue';
const savedCities = ref([]);
const getCities = async () => {
  if(localStorage.getItem("savedCities")){
    savedCities.value = JSON.parse(localStorage.getItem('savedCities'));
    const requests = [];
    savedCities.value.forEach((city) => {
      requests.push(
        axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=f9fe6d85c7041b439d343c8fdbb67159&units=imperial`)
      )
    });
    const weatherData = await Promise.all(requests);
    // flicker delay
    await new Promise((res)=> setTimeout(res,1000))
    weatherData.forEach((value,index)=>{
      savedCities.value[index].weather = value.data;
    });
  }
}
await getCities();
const router = useRouter();
const gotoCityView = (city) => {
  router.push({
    name : 'cityView',
    params : {state:city.state , city:city.city},
    query : {id:city.id, lat:city.coords.lat , lng:city.coords.lng},
  })
}
</script>

