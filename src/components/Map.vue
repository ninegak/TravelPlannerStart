<template>
  <div>
    <section>
      <div class="column">
        <div class="field">
          <input type="text" name="search" id="autocomplete" placeholder="Enter any address" v-model="address">
          <br>
        </div>
      </div>
    </section>

    <section>
      <div id="map"></div>
    </section>
    <p>Number of People?</p>
    <input type="number" name="numb" id="numb" v-model="people">
    <p>Budget</p>
    <input type="number" name="cost" id="cost" v-model="cost">
    <p>How long do you want to stay?</p>
    <input type="number" name="day" id="day" v-model="days">
    <p>which dates?</p>
    <input type="date" name="date" id="date" v-model="date">
    <button @click="getPlaceName" class="button" id="go">Go</button>
      <p v-if="placeName">Place Name: {{ placeName }}</p>
  </div>
</template>

<script>
import axios from 'axios';
export default {

  methods: {
    locatorButton() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            this.getAddressFrom(position.coords.latitude, position.coords.longitude);
            this.showUserLocationOnTheMap(position.coords.latitude, position.coords.longitude);
          },
          (error) => {
            console.log(error.message);
          }
        );
      } else {
        console.log("Your browser does not support geolocation.");
      }
    },
    getAddressFrom(lat, lng) {
      axios.get(`https://maps.googleapis.com/maps/api/geocode/json?latlng=${lat},${lng}&key=APIKEY`)
        .then((response) => {
          if (response.data.error_message) {
            console.log(response.data.error_message);
          } else {
            this.address = response.data.results[0].formatted_address;
          }
        })
        .catch((error) => {
          console.log(error.message);
        });
    },
    getPlaceName() {
      // Initialize the Google Maps Geocoder
      const geocoder = new google.maps.Geocoder();
      const self = this;
      // Geocode the user's input address
      geocoder.geocode({ address: this.address }, (results, status) => {
        if (status === google.maps.GeocoderStatus.OK) {
          if (results[0]) {
            // Get the place name from the geocoded result
            const placeName = results[0].formatted_address;
            self.placeName = placeName;

            this.placeName = placeName;
            this.$emit("data-updated", {
            placeName: this.placeName,
            people: this.people,
            cost: this.cost,
            days: this.days,
            date: this.date,
          });
          } else {
            console.error('No results found');
          }
        } else {
          console.error('Geocoder failed due to: ' + status);
        }
        
      });
      //console.log(this.address)
    },
    clearLocalStorage() {
      localStorage.clear();
  },
    showUserLocationOnTheMap(latitude, longitude) {
      let map = new google.maps.Map(document.getElementById("map"), {
        zoom: 15,
        center: new google.maps.LatLng(latitude, longitude),
        mapTypeId: google.maps.MapTypeId.ROADMAP
      });

      new google.maps.Marker({
        position: new google.maps.LatLng(latitude, longitude),
        map: map
      });
    }
  },
  
  data() {
    return {
      address: "",
      placeName: "",
      people: 0,
      cost: 0,
      days: 0,
      date: null,
    };
  },
  mounted() {
    let autocomplete = new google.maps.places.Autocomplete(
      document.getElementById("autocomplete"),
      {
        bounds: new google.maps.LatLngBounds(
          new google.maps.LatLng(18.796143, 98.979263)
        )
      }
    );
    autocomplete.addListener("place_changed", () => {
      let place = autocomplete.getPlace();
      this.showUserLocationOnTheMap(place.geometry.location.lat(), place.geometry.location.lng());
    });
    const storedPlaces = localStorage.getItem('places');
    if (storedPlaces) {
      this.places = JSON.parse(storedPlaces);
    }
  }
};
</script>

<style>
#map {
  height: 300px;
  background-color: firebrick;
  margin-top: 15px;
  margin-bottom: 15px;
  width: 100%;
}
</style>
