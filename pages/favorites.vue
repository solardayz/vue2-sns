<template>
  <div>
    <h1>Favorites</h1>
    <v-container>
      <v-row v-if="loading">
        <v-col cols="12" class="text-center">
          <v-progress-circular
            indeterminate
            color="primary"
          ></v-progress-circular>
        </v-col>
      </v-row>
      <v-row v-else>
        <v-col
          v-for="favorite in favorites"
          :key="favorite.id"
          cols="12"
          md="6"
          lg="4"
        >
          <v-card class="mb-4">
            <v-img :src="favorite.thumbnailUrl" height="200"></v-img>
            <v-card-title>{{ favorite.title }}</v-card-title>
            <v-card-subtitle>{{ favorite.id }}</v-card-subtitle>
            <!-- 필요한 추가 필드 -->
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      favorites: [],
      loading: false,
    };
  },
  async created() {
    this.loading = true;
    try {
      await this.fetchFavorites();
    } catch (error) {
      console.error("Error fetching favorites:", error);
    } finally {
      this.loading = false;
    }
  },
  methods: {
    async fetchFavorites() {
      const userId = Math.floor(Math.random() * 20) + 1;
      try {
        const response = await axios.get(
          `https://jsonplaceholder.typicode.com/albums/${userId}/photos`
        );
        this.favorites = response.data;
      } catch (error) {
        throw new Error("Failed to fetch favorites");
      }
    },
  },
};
</script>

<style scoped>
.text-center {
  text-align: center;
}
.mb-4 {
  margin-bottom: 1rem;
}
</style>
