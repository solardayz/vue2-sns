<template>
  <v-app dark>
    <v-container class="fill-height" fluid>
      <v-row justify="center" align="center">
        <v-col class="text-center">
          <v-img
            v-if="error.statusCode === 404"
            src="https://via.placeholder.com/150?text=404+Image"
            alt="404 Not Found Image"
            contain
            max-height="150"
          ></v-img>
          <v-img
            v-else
            src="https://via.placeholder.com/150?text=Error+Image"
            alt="Error Image"
            contain
            max-height="150"
          ></v-img>
          <v-card class="mt-5" outlined>
            <v-card-title class="headline">{{ errorMessage }}</v-card-title>
            <v-card-actions>
              <NuxtLink to="/">
                <v-btn color="primary" outlined> Home Page </v-btn>
              </NuxtLink>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
export default {
  name: "EmptyLayout",
  layout: "empty",
  props: {
    error: {
      type: Object,
      default: null,
    },
  },
  data() {
    return {
      pageNotFound: "404 Not Found",
      otherError: "An error occurred",
    };
  },
  computed: {
    errorMessage() {
      return this.error.statusCode === 404
        ? this.pageNotFound
        : this.otherError;
    },
  },
  head() {
    const title =
      this.error.statusCode === 404 ? this.pageNotFound : this.otherError;
    return {
      title,
    };
  },
};
</script>

<style scoped>
.v-img {
  margin-bottom: 20px;
}
.v-card-title {
  font-size: 24px;
}
.v-btn {
  margin-top: 20px;
}
</style>
