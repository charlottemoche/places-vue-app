<template>
  <div class="home">
    <h1>New Place</h1>
    <div>
      <input type="text" v-model="newPlaceName" placeholder="Name" />
      <br />
      <br />
      <input type="text" v-model="newPlaceAddress" placeholder="Address" />
      <br />
      <br />
      <button v-on:click="createPlace()">Add a Place</button>
      <br />
      <p>{{ errors }}</p>
    </div>
    <h1>Places</h1>
    <div v-for="place in places" v-bind:key="place.id">
      <p>Name: {{ place.name }}</p>
      <p>Address: {{ place.address }}</p>
      <br />
      <button v-on:click="showPlace(place)">More info</button>
      <br />
      <dialog>
        <form method="dialog">
          <h2>Place info</h2>
          <p>
            Name:
            <input type="text" v-model="currentPlace.name" />
          </p>
          <p>
            Address:
            <input type="text" v-model="currentPlace.address" />
          </p>
          <br />
          <br />
          <button v-on:click="updatePlace({ currentPlace })">Update Place</button>
          <button>Close</button>
        </form>
      </dialog>
      <br />
    </div>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
export default {
  data: function() {
    return {
      places: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {},
      errors: [],
    };
  },
  created: function() {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function() {
      axios.get("/api/places").then(response => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function() {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress,
      };
      axios
        .post("/api/places", params)
        .then(response => {
          console.log(response.data);
          this.places.unshift(response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function(place) {
      console.log(place);
      document.querySelector("dialog").showModal();
      this.currentPlace = place;
    },
    updatePlace: function(inputPlace) {
      var params = {
        name: inputPlace.name,
        address: inputPlace.address,
      };
      axios
        .patch(`/api/places/${inputPlace.id}`, params)
        .then(response => {
          console.log("Success", response.data);
        })
        .catch(error => {
          console.log("Error", error.response.data);
        });
    },
    destroyPlace: function(inputPlace) {
      axios.delete(`/api/places/${inputPlace.id}`).then(response => {
        console.log("Success", response.data);
        var index = this.places.indexOf(inputPlace);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>
