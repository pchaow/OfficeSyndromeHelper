<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
       <h2>Office Syndrome Helper</h2>
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://github.com/vuetifyjs/vuetify/releases/latest"
        target="_blank"
        text
      >
        <span class="mr-2">Latest Release</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <OfficeSyndrome/>
    </v-main>
    <v-footer padless>
      <v-col
        class="text-center"
        cols="12"
      >
        {{ new Date().getFullYear() }} — <strong>Chaow Porkaew and Zezembly Co., Ltd.</strong>
      </v-col>
    </v-footer>
  </v-app>
</template>

<script lang="ts">
import Vue from "vue";
import HelloWorld from "./components/HelloWorld.vue";
import OfficeSyndrome from "@/components/OfficeSyndrome.vue";

export default Vue.extend({
  name: "App",

  components: {
    OfficeSyndrome,
  },

  data: () => ({
    //
  }),
});
</script>
