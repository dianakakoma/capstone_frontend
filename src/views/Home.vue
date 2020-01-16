<template>
  <div class="home container">
    <h1>{{ message }}</h1>

    <h3>Add a new property</h3>
    <div>
      Address:
      <input type="text" v-model="new_address" />
      URL:
      <input type="text" v-model="new_url" />
      <button v-on:click="createProperty()">Submit</button>
    </div>

    <h3>Amenities</h3>
    <div>
      <span v-for="amenity in amenities">
        <input type="checkbox" v-bind:value="amenity.id" v-model="selectedAmenities" />
        <label>{{ amenity.description }}</label>
      </span>
      <br />
      <span>Checked names: {{ selectedAmenities }}</span>
    </div>

    <!-- <h3>Select your 'must have' amenities</h3>
    <select v-model="selected" multiple>
      <option>close to the lake</option>
      <option>public transportation</option>
      <option>designated parking</option>
      <option>bike room</option>
      <option>laundry in-unit</option>
      <option>laundry on premises</option>
      <option>wheel chair accessible</option>
      <option>laundry on premises</option>
      <option>3 bedrooms</option>
      <option>2 bathrooms</option>
      <option>"No Pets"</option>
      <option>Other....</option>
    </select>
    <br />
    <span>Selected: {{ selected }}</span>

    <h3>Add a new amenity to your list!!!</h3>
    <div>
      Amenity Description:
      <input type="text" v-model="new_amenity_user" />
      <button v-on:click="createAmenityUser()">Submit</button>
    </div>
    <h4>Delete an amenity from your must haves</h4>
    <button v-on:click="removeAmenityUser(amenity_user)">Delete this amenity</button> -->

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
                <button v-on:click="showProperty(property)">Property Details...</button>
                <div v-if="currentProperty === property">
                  <p>Here's the url: {{ property.url }}</p>
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

    <h1>Your Properties ({{ properties.length }} total)</h1>
    <div v-for="property in properties">
      <h2>{{ property.address }}</h2>
      <!-- <img v-bind:src="" v-bind:alt="property.address" /> -->
      <div>{{ property.image_url }}</div>

      <button v-on:click="showProperty(property)">Property Details...</button>
      <div v-if="currentProperty === property">
        <p>Here's the url: {{ property.url }}</p>
        <div v-for="image in property.images">
          <img v-bind:src="image.url" alt="" width="300" />
        </div>
      </div>

      <div v-if="currentProperty === property">
        Rating:
        <input type="text" v-model="property.rating" />
        Notes:
        <input type="text" v-model="property.notes" />
        Images:
        <input type="text" v-model="property.images" />
        <button v-on:click="updateProperty()">Submit</button>
      </div>
    </div>
  </div>
</template>

<style></style>

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
      new_amenity_user: "",
      selected: [],
      currentProperty: {},
      image_url: ""
    };
  },
  created: function() {
    axios.get("/api/properties").then(response => {
      this.properties = response.data;
      console.log("Properties", this.properties);
    });
    axios.get("/api/amenities").then(response => {
      this.amenities = response.data;
      console.log("amenities", this.amenities);
    });
    axios.get("/api/amenity_users").then(response => {
      this.selectedAmenities = response.data.map(amenity_user => amenity_user.id);
    });
  },
  methods: {
    createProperty: function() {
      var params = {
        address: this.new_address,
        url: this.new_url
      };
      axios.post("/api/properties", params).then(response => {
        this.properties.push(response.data);
        this.new_address = "";
        this.new_url = "";
      });
    },
    createAmenityUser: function() {
      var params = {
        amenity_users: this.new_amenity_users
      };
      axios.post("/api/amenity_users", params).then(response => {
        this.amenity_users.push(response.data);
        this.new_amenity_user = "";
      });
    },
    showProperty: function(property) {
      if (this.currentProperty === property) {
        this.currentProperty = {};
      } else {
        this.currentProperty = property;
      }
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
