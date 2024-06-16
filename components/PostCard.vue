<template>
  <v-card class="mx-auto" max-width="344">
    <v-img :src="photo.thumbnailUrl" height="200px"></v-img>
    <v-card-title>{{ photo.title }}</v-card-title>
    <v-card-subtitle>{{ photo.id }}</v-card-subtitle>
  </v-card>
</template>

<script>
export default {
  name: "PhotoCard",
  props: {
    photo: {
      type: Object,
      required: true,
    },
  },
  mounted() {
    console.log("PhotoCard props:", this.photo);
  },
};
</script>

<style scoped>
/* Add your styles here */
</style>
