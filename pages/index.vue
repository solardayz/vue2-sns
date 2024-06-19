<template>
  <v-container>
    <v-row justify="center">
      <v-col cols="12" md="8">
        <h1 class="text-center">Photos from Album 1</h1>
        <v-alert v-if="error" type="error" dismissible>
          {{ error }}
        </v-alert>
      </v-col>
    </v-row>
    <v-row justify="center" v-if="loading">
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
  </v-container>
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
      const userId = Math.floor(Math.random() * 20) + 1;
      try {
        this.loading = true;
        const response = await axios.get(
          `https://jsonplaceholder.typicode.com/albums/${userId}/photos`
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
.text-center {
  text-align: center;
}
</style>
