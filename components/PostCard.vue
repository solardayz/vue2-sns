<template>
  <v-card class="mx-auto my-4" max-width="344" elevation="2">
    <v-img
      :src="photo.thumbnailUrl"
      height="200px"
      class="white--text"
      gradient="to bottom, rgba(0,0,0,.1), rgba(0,0,0,.5)"
    >
      <v-card-title>{{ photo.title }}</v-card-title>
    </v-img>
    <v-card-subtitle class="pb-0">{{ photo.id }}</v-card-subtitle>
    <v-card-actions>
      <v-btn text color="primary"> View </v-btn>
    </v-card-actions>
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
.v-card-title {
  background: rgba(0, 0, 0, 0.5);
  padding: 16px;
  border-radius: 4px;
}
</style>
