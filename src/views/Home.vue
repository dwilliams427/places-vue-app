<template>
  <div class="home">
    <div>
      <h1>Add Pace!</h1>
      <input type="text" v-model="newPlaceName" placeholder="name" />
      <input type="text" v-model="newPlaceAddress" placeholder="address" />
      <button v-on:click="createPlaces">Create New Place</button>
    </div>

    <dialog id="place-details">
      <form method="dialog">
        <h1>Place Info!</h1>
        <p>
          title:
          <input type="text" v-model="currentPlace.name" />
        </p>
        <p>
          year:
          <input type="text" v-model="currentPlace.address" />
        </p>
        <button v-on:click="updatePlace(currentPlace.id)">Update Place</button>
        <button v-on:click="destroyPlace(currentPlace)">Destroy Place</button>
        <button>Close</button>
      </form>
    </dialog>

    <div>
      <h1>All Places!</h1>
      <div>
        <div v-for="place in places" v-bind:key="place.id">
          <p>
            <b>{{ place.name }} - {{ place.address }}</b>
            <button v-on:click="placeInfo(place)">More Info</button>
          </p>
        </div>
      </div>
    </div>
    <div>
      <ul>
        <li v-for="error in errors" v-bind:key="error">{{ error }}</li>
      </ul>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from "axios";

export default {
  data: function () {
    return {
      places: [],
      currentPlace: {},
      newPlaceName: "",
      newPlaceAddress: "",
      errors: [],
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("/api/places").then((response) => {
        this.places = response.data;
        console.log("all places: ", this.places);
      });
    },
    createPlaces: function () {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress,
      };
      axios
        .post("/api/places", params)
        .then((response) => {
          console.log("success", response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    placeInfo: function (place) {
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      var params = {
        name: place.name,
        addess: place.addess,
      };
      axios
        .patch("/api/places/" + place.id, params)
        .then((response) => {
          console.log("place updated", response);
          console.log("place id: " + place.id);
          this.currentPlace = {};
        })
        .catch((error) => {
          console.log("places update error", error.response);
        });
    },
    destroyPlace: function (place) {
      axios.delete("/api/places/" + place.id).then((response) => {
        console.log("place destroyed", response);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>
