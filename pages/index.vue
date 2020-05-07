<template>
  <v-row class="hero-container" align="center" justify="center">
    <v-col class="text-center" cols="12">
      <h1 class="display-4 mb-10">Web Radio</h1>
      <div class="display-2 mb-10">Prochain stream: {{ nextStreamDate }}</div>
      <v-btn
        fab
        x-large
        color="primary"
        :loading="state === 'loading'"
        @click="play()"
      >
        <v-icon v-if="state === 'stopped'">mdi-play</v-icon>
        <v-icon v-if="state === 'playing'">mdi-pause</v-icon>
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
<script>
import format from 'date-fns/format';
import { parseISO } from 'date-fns';
import { fr } from 'date-fns/locale';

export default {
  data: () => {
    return {
      nextStreamDate: format(parseISO('2020-05-04T20:00:00.000Z'), 'PPp', {
        locale: fr
      }),
      audio: new Audio({
        src: 'https://djset.wmarques.com/stream'
      }),
      state: 'stopped'
    };
  },
  created() {},
  methods: {
    play() {
      if (this.audio.playing()) {
        this.audio.stop();
        this.state = 'stopped';
      } else {
        this.audio.play();
        this.state = 'playing';
      }
    }
  }
};
</script>
