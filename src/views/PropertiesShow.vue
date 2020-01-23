<template>
  <div id="main-wrapper">
    <div class="container">
      <div class="row gtr-200">
        <div class="col-4 col-12-medium">
          <!-- Sidebar -->
          <div id="sidebar">
            <section class="widget thumbnails">
              <h3>My Photos</h3>
              <div class="grid">
                <div class="row gtr-50">
                  <div v-for="image in property.images" class="col-6">
                    <a v-bind:href="image.url" class="image fit"><img v-bind:src="image.url" alt="" /></a>
                  </div>
                </div>
              </div>
              <!--<a href="#" class="button icon fa-file-alt">More</a> -->
            </section>
            <!--upload user photo -->
            <form v-on:submit.prevent="submit()">
              <h4>Add new photos to your gallery</h4>

              <div>
                Image:
                <input type="file" v-on:change="setFile($event)" ref="fileInput" />
              </div>
              <input type="submit" value="Submit Your Photos" />
            </form>
          </div>
        </div>
        <div class="col-8 col-12-medium imp-medium">
          <!-- Content -->
          <!-- mapbox -->
          <div id="map" style="width: 600px; height: 300px;"></div>
          <div id="content">
            <section class="last">
              <h2>{{ property.address }}</h2>
              <h2 style="color:green">${{ property.rent }}</h2>
              Original listing:
              <a v-bind:href="property.url" target="_blank">{{ property.url }}</a>

              <div>Upload date: {{ property.created_at }}</div>

              <div>Last update on {{ property.updated_at }}</div>

              <form v-on:submit.prevent="updateProperty()">
                <div class="form-group">
                  <h3>
                    <label>Rating: {{ property.rating }}</label>
                    <img v-if="property.rating == 1" class="img-star" src="../assets/1star.png" alt="" />
                    <img v-if="property.rating == 2" class="img-star" src="../assets/2stars.png" alt="" />
                    <img v-if="property.rating == 3" class="img-star" src="../assets/3stars.png" alt="" />
                    <img v-if="property.rating == 4" class="img-star" src="../assets/4stars.png" alt="" />
                    <img v-if="property.rating == 5" class="img-star" src="../assets/5stars.png" alt="" />
                  </h3>
                  <input type="number" min="0" max="5" class="form-control" v-model="property.rating" />
                </div>
                <div class="form-group">
                  <h4><label>My notes and/or questions:</label></h4>
                  <input type="text" class="form-control" v-model="property.notes" />
                </div>
                <br />
                <input type="submit" class="btn btn-primary" value="Update Information" />
              </form>

              <br />
              <a href="/" class="button icon solid fa-arrow-circle-right">Back to all properties</a>
            </section>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
.img-star {
  height: 50px;
}
</style>

<script>
/* global mapboxgl, mapboxSdk */
import axios from "axios";

export default {
  data: function() {
    return {
      property: {},
      image: ""
    };
  },
  mounted: function() {
    axios.get("/api/properties/" + this.$route.params.id).then(response => {
      this.property = response.data;
      this.setupMap();
    });
  },
  methods: {
    setupMap: function() {
      mapboxgl.accessToken =
        "pk.eyJ1IjoiZGlrYWtvbWEiLCJhIjoiY2s1bGp4aHM4MDl6OTNucGhncGc5MTQwMyJ9.1CSzGkIaH20XGn4jYKeOnw";
      var mapboxClient = mapboxSdk({ accessToken: mapboxgl.accessToken });

      mapboxClient.geocoding
        .forwardGeocode({
          query: this.property.address,
          autocomplete: false,
          limit: 1
        })
        .send()
        .then(function(response) {
          if (response && response.body && response.body.features && response.body.features.length) {
            var feature = response.body.features[0];

            var map = new mapboxgl.Map({
              container: "map",
              style: "mapbox://styles/mapbox/streets-v11",
              center: feature.center,
              zoom: 12
            });

            new mapboxgl.Marker().setLngLat(feature.center).addTo(map);
          }
        });
    },
    updateProperty: function() {
      var params = {
        rating: this.property.rating,
        notes: this.property.notes
      };
      axios.patch("/api/properties/" + this.property.id, params).then(response => {
        console.log("success");
      });
    },
    setFile: function(event) {
      if (event.target.files.length > 0) {
        this.image = event.target.files[0];
      }
    },
    submit: function() {
      var formData = new FormData();
      formData.append("image", this.image);
      formData.append("property_id", this.property.id);

      axios.post("/api/images", formData).then(response => {
        this.$refs.fileInput.value = "";
        this.property.images.push(response.data);
      });
    }
  }
};
</script>
