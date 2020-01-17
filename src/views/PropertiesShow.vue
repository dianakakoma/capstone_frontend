<template>
  <div id="main-wrapper">
    <div class="container">
      <div class="row gtr-200">
        <div class="col-4 col-12-medium">
          <!-- Sidebar -->
          <div id="sidebar">
            <section class="widget thumbnails">
              <h3>Interesting stuff</h3>
              <div class="grid">
                <div class="row gtr-50">
                  <div v-for="image in property.images" class="col-6">
                    <a href="#" class="image fit"><img v-bind:src="image.url" alt="" /></a>
                  </div>
                </div>
              </div>
              <a href="#" class="button icon fa-file-alt">More</a>
            </section>
          </div>
        </div>
        <div class="col-8 col-12-medium imp-medium">
          <!-- Content -->
          <div id="content">
            <section class="last">
              <h2>{{ property.address }}</h2>
              <a v-bind:href="property.url" target="_blank">{{ property.url }}</a>
              <p>{{ property.create_date }}</p>

              <form v-on:submit.prevent="updateProperty()">
                <div class="form-group">
                  <label>Rating:</label>
                  <input type="text" class="form-control" v-model="property.rating" />
                </div>
                <div class="form-group">
                  <label>Notes:</label>
                  <input type="text" class="form-control" v-model="property.notes" />
                </div>
                <br />
                <input type="submit" class="btn btn-primary" value="Submit" />
              </form>

              <br />
              <a href="/" class="button icon solid fa-arrow-circle-right">Back to all properties</a>
            </section>
          </div>
        </div>
      </div>
    </div>

    {{ property.create_date }}
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      property: {}
    };
  },

  created: function() {
    axios.get("/api/properties/" + this.$route.params.id).then(response => {
      this.property = response.data;
    });
  },
  methods: {
    updateProperty: function() {
      var params = {
        rating: this.property.rating,
        notes: this.property.notes
      };
      axios.patch("/api/properties/" + this.property.id, params).then(response => {
        console.log("success");
      });
    }
  }
};
</script>
