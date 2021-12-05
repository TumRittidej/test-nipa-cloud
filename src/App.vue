<template>
  <v-app>
    <the-navbar></the-navbar>
    <router-view></router-view>
  </v-app>
</template>

<script>
import TheNavbar from "@/components/layout/TheNavbar.vue";

export default {
  components: {
    TheNavbar,
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Prompt", sans-serif;
  scroll-behavior: smooth;
}
</style>