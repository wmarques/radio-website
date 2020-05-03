<template>
  <v-row class="hero-container" align="center" justify="center">
    <v-col class="text-center" cols="12">
      <h1 class="display-4 mb-10">Web Radio</h1>
      <v-btn fab x-large color="primary">
        <v-icon>mdi-play</v-icon>
      </v-btn>
    </v-col>
  </v-row>
</template>
<style scoped>
.hero-container {
  width: 100%;
  height: 100vh; /* if you don't want it to take up the full screen, reduce this number */
  overflow: hidden;
  background-size: cover;
  background: radial-gradient(
      ellipse at center,
      rgba(0, 0, 0, 0) 0%,
      rgba(0, 0, 0, 0) 37%,
      rgba(0, 0, 0, 0.65) 100%
    ),
    url('https://cdn.vuetifyjs.com/images/backgrounds/vbanner.jpg') no-repeat
      center center scroll;
}
</style>
