<template>
  <div class="home container">
    <h1>{{ message }}</h1>

    <h3>Add a new property</h3>
    <form v-on:submit.prevent="createProperty()">
      <div class="form-group">
        Address:
        <input type="text" class="form-control" v-model="new_address" />
      </div>
      URL:
      <input type="text" v-model="new_url" />
      $Rent:
      <input type="text" v-model="rent" />
      <button type="submit">Submit</button>
    </form>
    <h3>Your must-have amenities list!</h3>
    <div>
      <span v-for="amenity in amenities">
        <input
          class="amenity-checkbox"
          v-bind:id="`checkbox-${amenity.id}`"
          type="checkbox"
          v-bind:value="amenity.id"
          v-model="selectedAmenities"
        />

        <label v-bind:for="`checkbox-${amenity.id}`">{{ amenity.description }}</label>
        |
      </span>
      <button v-on:click="updateUserAmenities()">{{ saveButtonText }}</button>
    </div>

    <!-- Features -->
    <div id="features-wrapper">
      <div class="container">
        <div class="row">
          <div v-for="property in properties" class="col-4 col-12-medium">
            <!-- Box -->
            <section class="box feature">
              <a href="#" class="image featured"><img v-bind:src="property.main_image_url" alt="" /></a>
              <div class="inner">
                <header>
                  <h2>{{ property.address }}</h2>
                  <h2>${{ property.rent }}</h2>
                  <div>{{ property.image_url }}</div>
                </header>
                <!--ratings-->
                <div class="form-group">
                  <h3>
                    <label>Rating: {{ property.rating }}</label>
                    <!--
                    <img
                      v-if="property.rating == 1"
                      class="img-star; image fit"
                      src="../assets/1star.png"
                      alt=""
                      height="15px"
                    />
                    <img v-if="property.rating == 2" class="img-star; image fit" src="../assets/2stars.png" alt="" />
                    <img v-if="property.rating == 3" class="img-star; image fit" src="../assets/3stars.png" alt="" />
                    <img v-if="property.rating == 4" class="img-star; image fit" src="../assets/4stars.png" alt="" />
                    <img v-if="property.rating == 5" class="img-star; image fit" src="../assets/5stars.png" alt="" />
                  --></h3>
                  <!--<input type="number" min="0" max="5" class="form-control" v-model="property.rating" /> -->
                </div>
                <!--details button-->
                <button v-on:click="showProperty(property)">Property Details...</button>
                <div v-if="currentProperty === property">
                  <h3>Original listing: {{ property.url }}</h3>
                  <div v-for="image in property.images">
                    <img v-bind:src="image.url" alt="" width="300" />
                  </div>
                </div>
              </div>
            </section>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
section.box.feature img {
  height: 250px;
  object-fit: cover;
}
input[type="checkbox"].amenity-checkbox {
  -webkit-appearance: checkbox;
  appearance: checkbox;
}
</style>

<script>
import axios from "axios";
export default {
  data: function() {
    return {
      message: "Welcome to Mooove!",
      properties: [],
      amenities: [],
      selectedAmenities: [],
      new_address: "",
      new_url: "",
      rent: "",
      new_amenity_user: "",
      selected: [],
      currentProperty: {},
      image_url: "",
      saveButtonText: "Save Your Amenities"
    };
  },
  created: function() {
    this.message = `Welcome ${localStorage.name}!`;
    axios.get("/api/properties").then(response => {
      this.properties = response.data;
      console.log("Properties", this.properties);
    });
    axios.get("/api/amenities").then(response => {
      this.amenities = response.data;
      console.log("amenities", this.amenities);
    });
    axios.get("/api/amenity_users").then(response => {
      this.selectedAmenities = response.data.map(amenity_user => amenity_user.amenity_id);
    });
  },

  methods: {
    createProperty: function() {
      var params = {
        address: this.new_address,
        url: this.new_url,
        rent: this.rent
      };
      axios.post("/api/properties", params).then(response => {
        this.properties.push(response.data);
        this.new_address = "";
        this.new_url = "";
        this.rent = "";
      });
    },
    updateUserAmenities: function() {
      this.saveButtonText = "Saving...";
      var params = {
        amenity_ids: this.selectedAmenities
      };
      axios.post("/api/amenity_users/all", params).then(response => {
        console.log(response);
        setTimeout(() => {
          this.saveButtonText = "Save Your Amenities";
        }, 750);
      });
      console.log();
    },
    showProperty: function(property) {
      this.$router.push("/properties/" + property.id);
      // if (this.currentProperty === property) {
      //   this.currentProperty = {};
      // } else {
      //   this.currentProperty = property;
      // }
    },
    updateProperty: function(property) {
      var params = {
        rating: property.rating,
        notes: property.notes,
        image: property.image_url
      };
      axios.patch("/api/properties" + property.id, params).then(response => {
        this.currentProperty = {};
      });
    },
    removeAmenityUser: function(amenity) {
      axios.delete("/amenity_users/" + amenity_user.id).then(response => {
        var index = this.amenities.indexOf(amenity_users);
        this.amenity_users.splice(index, 1);
      });
    }
  }
};
</script>
