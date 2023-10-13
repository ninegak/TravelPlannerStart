<template>
<div class="startplan">
  <div id="app-modal" class="app-modal">
    <p>Start Your Plan</p>
    <button class="button" @click="showModal = true">+</button>
  </div>
 <transition name="fade" appear>
  <div class="modal-overlay" v-if="showModal" @click="showModal = false"></div>
 </transition>
 <transition name="slide" appear>
  <div class="modal" v-if="showModal">
    <div class="container">
    <div class="task">
      <div class="title">
        <h1> Plan Your Trip!</h1>
      </div>
      <Map @data-updated="handleDataUpdate"/><br>
      
    </div>
  </div>
   <button class="button" id="close" @click="showModal = false">
    Close Modal
   </button>
  </div>
 </transition>
 </div>
 <h2>Planning</h2>
 <div class="display">
  <div class="card" v-for="(place, index) in places" :key="index">
      <p>{{ place.placeName }}</p>
      <p>Number of People: {{ place.people }}</p>
      <p>Budget: {{ place.cost }} $</p>
      <p>Stay for: {{ place.days }} days</p>
      <p>On: {{ place.date }}</p>
      <button @click="() => deletePlace(index)" class="button">Delete</button>
    </div>
    </div>
</template>

<script>
import Map from './Map.vue';

export default {
  name: "modal",
  props: ['tasks', 'placeName'],
  components: {
    Map,
  },
  data () {
    return {
      showModal: false,
      placeName: "",
      places: [],
  }
 },
 created() {
    // Load data from localStorage when the component is created
    const savedPlaces = localStorage.getItem('savedPlaces');
    if (savedPlaces) {
      this.places = JSON.parse(savedPlaces);
    }
  },
 methods: {
  saveData() {
      // Save data to localStorage
      localStorage.setItem('savedPlaces', JSON.stringify(this.places));
    },
  deletePlace(index) {
      // Remove the place at the specified index
      this.places.splice(index, 1);
      console.log(this.places)
      console.log(index)
      this.saveData();
    },
    handleDataUpdate(data) {
      // Receive the emitted data and update your component's data properties
      this.places.push({
        placeName: data.placeName,
        people: data.people,
        cost: data.cost,
        days: data.days,
        date: data.date,
      });
      this.saveData();
      console.log(this.placeName);
    }
  },
  

}
</script>

<style>
* {
 margin: 0;
 padding: 0;
 box-sizing: border-box;
}
</style>