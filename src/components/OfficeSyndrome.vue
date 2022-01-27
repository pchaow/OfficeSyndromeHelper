<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12">
        <div :class="{
          'display-1' : true,
          'default--text' : status == 'Idle',
          'primary--text' : !overtime && status != 'Idle',
          'red--text' : overtime && status != 'Idle'
        }">{{ status }}
        </div>
      </v-col>
      <v-col cols="12">
        <v-progress-circular
          :rotate="-90"
          :size="300"
          :width="15"
          :value="percent"
          :color="overtimeColor"
        >
          <v-row>
            <h1>{{ remainingTime }}</h1>
          </v-row>


        </v-progress-circular>
      </v-col>
      <v-col cols="12">
        <v-btn color="primary" style="margin-right: 1em" @click="working">Working</v-btn>
        <v-btn color="error" @click="resting">Resting</v-btn>
      </v-col>
      <v-col cols="12">
        <v-btn color="default" style="" @click="stop">Stop</v-btn>
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import Vue from "vue";

const STATUS = {
  working: "Working",
  resting: "Resting",
  stop: "Idle"
};

const publicPath = "/OfficeSyndromeHelper";

export default Vue.extend({
  name: "OfficeSyndrome",
  computed: {
    overtimeColor: function() {
      if (this.status == STATUS.stop) {
        return "default";
      } else {
        return !this.overtime ? "primary" : "red";
      }

    },
    remainingTime: function() {
      return new Date(this.remainingSeconds * 1000).toISOString().substr(11, 8);
    },
    percent: function() {
      return this.remainingSeconds / this.totalSeconds * 100;
    }
  },
  data: () => ({
    publicPath: process.env.BASE_URL,
    totalRestingSeconds: 5*60,
    totalWorkingSeconds: 45*60,
    totalSeconds: 10,
    remainingSeconds: 10, //45 min * 60 seconds,
    intervalObj: 0,
    status: STATUS.stop,
    overtime: false,
    audio: {
      start_working: new Audio(`${publicPath}/audio/start_working.mp3`),
      working_timeout: new Audio(`${publicPath}/audio/working_timeout.mp3`),
      start_resting: new Audio(`${publicPath}/audio/start_resting.mp3`),
      resting_timeout: new Audio(`${publicPath}/audio/resting_timeout.mp3`)
    }
  }),
  methods: {
    workingFunc: function() {
      if (this.overtime == false) {
        this.remainingSeconds--;
        if (this.remainingSeconds == 0) {
          this.overtime = true;
          this.audio.working_timeout.play();
        }
      } else {
        this.remainingSeconds++;
        this.totalSeconds = 1;
        if (this.remainingSeconds % 60 == 0) {
          this.audio.working_timeout.play();
        }
      }
    },
    restingFunc: function() {
      if (this.overtime == false) {
        this.remainingSeconds--;
        if (this.remainingSeconds == 0) {
          this.overtime = true;
          this.audio.resting_timeout.play();

        }
      } else {
        this.remainingSeconds++;
        this.totalSeconds = 1;
        if (this.remainingSeconds % 60 == 0) {
          this.audio.resting_timeout.play();
        }
      }
    },
    startInterval: function() {
      let func = this.status == STATUS.working ? this.workingFunc : this.restingFunc;
      return setInterval(func, 1000);
    },
    resetInterval: function() {
      clearInterval(this.intervalObj);
      this.overtime = false;
    },
    stop: function() {
      this.resetInterval();
      this.status = STATUS.stop;
    },
    working: function() {
      this.audio.start_working.play();
      this.resetInterval();
      this.status = STATUS.working;
      this.remainingSeconds = this.totalWorkingSeconds;
      this.totalSeconds = this.totalWorkingSeconds;
      this.intervalObj = this.startInterval();
    },
    resting: function() {
      this.audio.start_resting.play();
      this.resetInterval();
      this.status = STATUS.resting;
      this.remainingSeconds = this.totalRestingSeconds;
      this.totalSeconds = this.totalRestingSeconds;
      this.intervalObj = this.startInterval();
    }
  }
});
</script>
