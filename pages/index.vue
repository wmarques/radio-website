<template>
  <v-row class="hero-container" align="center" justify="center">
    <v-col class="text-center" cols="12">
      <div class="display-3 mb-10">La radio de William</div>
      <div class="display-1 mb-10">Prochain live: {{ nextStreamDate }}</div>
      <v-btn
        fab
        x-large
        color="primary"
        :loading="state === 'loading'"
        @click="play()"
      >
        <v-icon v-if="state === 'stopped'">{{ icons.play }}</v-icon>
        <v-icon v-if="state === 'playing'">{{ icons.stop }}</v-icon>
      </v-btn>
      <v-row align="center" justify="center" class="mt-10">
        <v-col class="text-center" cols="12" sm="6" md="4">
          <v-slider
            v-model="volume"
            :prepend-icon="icons.volume"
            min="0"
            max="100"
          ></v-slider>
        </v-col>
      </v-row>
    </v-col>
    <audio
      id="player"
      src="https://djset.wmarques.com/stream"
      preload="auto"
      style="display: none;"
    ></audio>
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
      rgba(0, 0, 0, 0) 0%,
      rgba(0, 0, 0, 0.65) 100%
    ),
    url('~assets/bg.jpg') no-repeat center center scroll;
}
</style>

<script>
import format from 'date-fns/format';
import { parseISO } from 'date-fns';
import { fr } from 'date-fns/locale';
import { mdiPlay, mdiStop, mdiVolumeHigh } from '@mdi/js';

export default {
  data: () => {
    return {
      audio: null,
      icons: {
        play: mdiPlay,
        stop: mdiStop,
        volume: mdiVolumeHigh
      },
      volume: 100,
      nextStreamDate: format(parseISO('2020-09-02T19:30:00.000Z'), 'PPp', {
        locale: fr
      }),
      state: 'stopped'
    };
  },

  watch: {
    volume() {
      this.audio.volume = this.volume / 100;
    }
  },
  mounted() {
    this.audio = document.getElementById('player');
  },
  methods: {
    play() {
      if (this.state === 'loading') {
        return;
      }

      if (this.state === 'playing') {
        this.audio.pause();
        this.state = 'stopped';
      } else {
        this.audio.load();
        this.audio.play().then(
          () => {
            this.state = 'playing';
          },
          () => {
            this.state = 'stopped';
          }
        );
        this.state = 'loading';
      }
    }
  }
};
</script>
