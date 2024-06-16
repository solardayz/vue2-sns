<template>
  <v-app>
    <v-app-bar :clipped-left="clipped" fixed app>
      <template>
        <v-row justify="space-around">
          <v-btn icon @click.stop="clipped = !clipped">
            <v-icon>mdi-boxing-glove</v-icon>
          </v-btn>
        </v-row>
      </template>
    </v-app-bar>
    <v-main>
      <v-container> <Nuxt /> </v-container>
    </v-main>
    <v-footer fixed app>
      <template>
        <v-bottom-navigation v-model="value">
          <v-btn value="recent" to="/">
            <!-- Link to the "Recent" view -->
            <span>Recent</span>
            <v-icon>mdi-history</v-icon>
          </v-btn>
          <v-btn value="favorites" to="/favorites">
            <!-- Link to the "Favorites" view -->
            <span>Favorites</span>
            <v-icon>mdi-heart</v-icon>
          </v-btn>
          <v-btn value="profile" to="/profile">
            <!-- Link to the "Profile" view -->
            <span>Profile</span>
            <v-icon>mdi-account</v-icon>
          </v-btn>
        </v-bottom-navigation>
      </template>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: "DefaultLayout",
  data() {
    return {
      value: "recent",
      clipped: false,
      drawer: false,
      fixed: false,
      tab: null,
      text: "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
      items: [
        {
          icon: "mdi-apps",
          title: "Welcome",
          to: "/",
        },
        {
          icon: "mdi-chart-bubble",
          title: "Inspire",
          to: "/inspire",
        },
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: "SBC",
    };
  },
};
</script>
