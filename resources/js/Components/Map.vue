<script setup>
/* eslint-disable no-undef */
import { ref , onMounted } from 'vue'
import { Loader } from '@googlemaps/js-api-loader'
import BreezeButton from '@/Components/Button.vue';
import BreezeInput from '@/Components/Input.vue';
import BreezeLabel from '@/Components/Label.vue';
import { Head , useForm } from '@inertiajs/inertia-vue3';

const GOOGLE_MAPS_API_KEY = 'AIzaSyDf43lPdwlF98RCBsJOFNKOkoEjkwxb5Sc'
const form = useForm({
    SWlat: null,
    SWlng: null,
    NElng: null,
    NElat: null,
    name:null,
});
    // const { coords } = useGeolocation()
    // const currPos = computed(() => ({
    //   lat: coords.value.latitude,
    //   lng: coords.value.longitude
    // }))
    // const otherPos = ref(null)

    let bounds = {
      north: 26.364594618280247,
      south: 26.363898447512295,
      east: 50.060623119221916,
      west: 49.960623119221916,
    };
    
    const dxoo = ref(null)
    const loader = new Loader({ apiKey: GOOGLE_MAPS_API_KEY })
    const mapDiv = ref(null)
    let map = ref(null)
    let rectangle = ref(null)
    const clickListener = ref(null)

    onMounted(async () => {
      await loader.load()
      map = new google.maps.Map(mapDiv.value, {
        center: { lat: 25.5452, lng: 50.5389 },
        zoom: 7
      })

      rectangle = new google.maps.Rectangle({
        bounds: bounds,
        editable: true,
        draggable: true,
      })
      rectangle.setMap(map)

      clickListener = new google.maps.event.addListener(rectangle,"bounds_changed", () => { 
        
        form.SWlat=rectangle.getBounds().getSouthWest().lat()
        form.SWlng=rectangle.getBounds().getSouthWest().lng()

        form.NElng=rectangle.getBounds().getNorthEast().lat()
        form.NElat=rectangle.getBounds().getNorthEast().lng()

        }
      )

    })


</script>

<template>
  <div class="bg-white border rounded shadow">
    <div class="border-b p-3">
    <BreezeButton form="element" class="ml-4" :class="{ 'opacity-25': form.processing }" :disabled="form.processing">
      cteate
    </BreezeButton>
    </div>

    <div class="px-5 mt-2 flex flex-col items-start">  
      <BreezeLabel for="name" value="title location" />
      <BreezeInput v-model="form.name" type="text" id="name" class="mt-1 block w-full" />
    </div>

    <div class="p-5 relative overflow-x-auto shadow-md sm:rounded-lg">

      <form class="sm:flex sm:items-start" id="element" >
        <BreezeInput v-model="form.SWlat" type="number" class="mt-1 block w-full" readonly />
        <BreezeInput v-model="form.SWlng" type="number" class="mt-1 block w-full" readonly />
        <BreezeInput v-model="form.NElng" type="number" class="mt-1 block w-full" readonly />
        <BreezeInput v-model="form.NElat" type="number" class="mt-1 block w-full" readonly />
      </form>

      <div ref="mapDiv" style="width: 100%; height: 80vh" />
    </div>
    
  </div>
</template>
