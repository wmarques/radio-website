<template>
  <v-row class="hero-container" align="center" justify="center">
    <v-col class="text-center" cols="12">
      <div class="display-3 mb-10">La radio de William</div>
      <div v-if="playerState !== 'replay'" class="display-1 mb-10">
        Live nouvel an <br />
        23h30 - 00h30
      </div>
      <div v-if="playerState === 'replay'" class="display-1 mb-10">
        Replay du live nouvel an
      </div>
      <div v-if="playerState !== 'hidden'">
        <div class="headline">Ambiance</div>
        <v-btn-toggle
          v-model="song"
          class="mb-10"
          color="primary"
          group
          mandatory
        >
          <v-btn value="chill" width="120">
            Party
          </v-btn>

          <v-btn value="hard" width="120">
            Party Hard
          </v-btn>
        </v-btn-toggle>
        <div>
          <v-btn
            fab
            x-large
            color="primary"
            :loading="state === 'loading'"
            @click="play()"
          >
            <v-icon v-if="state === 'stopped' && playerState !== 'replay'"
              >{{ icons.play }}
            </v-icon>
            <v-icon v-if="state === 'playing' && playerState !== 'replay'"
              >{{ icons.stop }}
            </v-icon>
            <v-icon v-if="playerState === 'replay'"
              >{{ icons.download }}
            </v-icon>
          </v-btn>
        </div>
        <v-row
          v-if="playerState !== 'replay'"
          align="center"
          justify="center"
          class="mt-10"
        >
          <v-col class="text-center" cols="12" sm="6" md="4">
            <v-slider
              v-model="volume"
              :prepend-icon="icons.volume"
              min="0"
              max="100"
            ></v-slider>
          </v-col>
        </v-row>
      </div>
    </v-col>
    <!-- <audio
      id="player"
      src="https://djset.wmarques.com/stream"
      preload="auto"
      style="display: none;"
    ></audio> -->

    <audio
      id="player"
      src="https://www.mboxdrive.com/Mix%20trkl%20(online-audio-converter.com).mp3"
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
import {
  parseISO,
  differenceInSeconds,
  isBefore,
  isWithinInterval,
  addSeconds
} from 'date-fns';
import { fr } from 'date-fns/locale';
import { mdiPlay, mdiStop, mdiVolumeHigh, mdiDownload } from '@mdi/js';
import colors from 'vuetify/es5/util/colors';

export default {
  data: () => {
    return {
      song: 'chill',
      audio: null,
      playerState: 'hidden',
      icons: {
        play: mdiPlay,
        stop: mdiStop,
        volume: mdiVolumeHigh,
        download: mdiDownload
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
    },
    song() {
      this.state = 'stopped';
      if (this.song === 'chill') {
        this.$vuetify.theme.currentTheme.primary = colors.blue;
        this.audio.src =
          'https://www.mboxdrive.com/Mix%20trkl%20(online-audio-converter.com).mp3';
      } else {
        this.$vuetify.theme.currentTheme.primary = colors.red;
        this.audio.src =
          'https://www.mboxdrive.com/mix%20hardcore%20(online-audio-converter.com).mp3';
      }
    }
  },
  mounted() {
    this.audio = document.getElementById('player');
    this.streamStart = new Date(2020, 11, 31, 23, 30);

    this.interval = setInterval(() => {
      const now = new Date();
      if (Number.isNaN(this.audio.duration)) {
        return;
      }
      if (isBefore(now, this.streamStart)) {
        this.playerState = 'hidden';
      } else if (
        isWithinInterval(now, {
          start: this.streamStart,
          end: addSeconds(this.streamStart, this.audio.duration)
        })
      ) {
        this.playerState = 'stream';
      } else {
        this.playerState = 'replay';
      }
    }, 1000);
  },
  beforeDestroy() {
    clearInterval(this.interval);
  },
  methods: {
    play() {
      if (this.playerState === 'replay') {
        window.open(this.audio.src);
        return;
      }
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
            this.audio.currentTime = differenceInSeconds(
              new Date(),
              this.streamStart
            );

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
