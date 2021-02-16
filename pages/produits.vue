<template>
  <div id="app">
    <div
      v-if="!registerActive"
      v-bind:class="{ error: emptyFields }"
    >
      <a href="#" @click="registerActive = !registerActive">
        Ajouter un produit</a
      >
      <seeproduct />
    </div>

    <div v-else >
      <a href="#" @click="registerActive = !registerActive"
        >Voir les produits</a
      >
      <addproduct />
    </div>
  </div>
</template>

<script>
import axios from "axios";
import addproduct from "@/components/addproduct";
import seeproduct from "@/components/seeproduct";

export default {
  components: {
    addproduct,
    seeproduct,
  },
  data() {
    return {
      registerActive: false,

      token: "",
    };
  },
  mounted() {},
  methods: {},
};
</script>

<style>
</style>