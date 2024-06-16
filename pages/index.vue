<template>
  <div>
    <h1>Photos from Album 1</h1>
    <div v-if="error" class="error">{{ error }}</div>
    <v-row v-if="loading">
      <v-col cols="12" class="text-center">
        <v-progress-circular
          indeterminate
          color="primary"
        ></v-progress-circular>
      </v-col>
    </v-row>
    <v-container v-else>
      <v-row>
        <v-col v-for="photo in photos" :key="photo.id" cols="12" sm="6" md="4">
          <post-card :photo="photo"></post-card>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
import axios from "axios";
import PostCard from "../components/PostCard.vue";

export default {
  components: { PostCard },
  data() {
    return {
      photos: [],
      loading: true,
      error: null,
    };
  },
  created() {
    this.fetchPhotos();
  },
  methods: {
    async fetchPhotos() {
      try {
        this.loading = true;
        const response = await axios.get(
          "https://jsonplaceholder.typicode.com/albums/1/photos"
        );
        this.photos = response.data;
        this.loading = false;
      } catch (error) {
        this.error = "Failed to load photos";
        console.error(error);
      } finally {
        this.loading = false;
      }
    },
  },
};
</script>

<style scoped>
/* Add your styles here */
.error {
  color: red;
}
</style>
