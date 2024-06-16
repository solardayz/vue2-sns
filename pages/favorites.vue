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
            <!-- Add additional fields as needed -->
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
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
      const userId = Math.floor(Math.random() * 20) + 1;
      const response = await fetch(
        `https://jsonplaceholder.typicode.com/albums/${userId}/photos`
      );
      if (!response.ok) {
        throw new Error("Failed to fetch favorites");
      }
      this.favorites = await response.json();
    } catch (error) {
      console.error("Error fetching favorites:", error);
    } finally {
      this.loading = false;
    }
  },
};
</script>

<style scoped>
/* Add scoped styles here */
</style>
