<template>
  <v-card v-if="user" class="profile-card">
    <v-img :src="user.avatar" class="avatar" contain></v-img>
    <v-card-title>{{ user.name }}</v-card-title>
    <v-card-subtitle>{{ user.username }}</v-card-subtitle>
    <v-card-text>
      <p>Email: {{ user.email }}</p>
      <p>Phone: {{ user.phone }}</p>
      <p>
        Website: <a :href="user.website" target="_blank">{{ user.website }}</a>
      </p>
      <p>
        Address: {{ user.address.street }}, {{ user.address.city }},
        {{ user.address.zipcode }}
      </p>
    </v-card-text>
  </v-card>
  <v-alert v-else-if="error" type="error">{{ error }}</v-alert>
  <v-progress-circular
    v-else
    size="64"
    indeterminate
    color="primary"
  ></v-progress-circular>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      user: null,
      error: null,
    };
  },
  async created() {
    await this.fetchUser();
  },
  methods: {
    async fetchUser() {
      const userId = Math.floor(Math.random() * 10) + 1;
      try {
        const response = await axios.get(
          `https://jsonplaceholder.typicode.com/users/${userId}`
        );
        this.user = response.data;
        this.user.avatar = this.generateAvatarUrl(this.user.email);
      } catch (error) {
        this.error = "Failed to fetch user profile";
        console.error(error);
      }
    },
    generateAvatarUrl(email) {
      return `https://i.pravatar.cc/150?u=${email}`;
    },
  },
};
</script>

<style scoped>
.profile-card {
  max-width: 400px;
  margin: auto;
  padding: 20px;
  text-align: center;
}

.avatar {
  width: 150px;
  height: 150px;
  border-radius: 50%;
  margin: auto;
  margin-bottom: 20px;
}
</style>
