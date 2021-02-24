<template>
  <div id="app">
    <div
      v-if="!registerActive"
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
import addproduct from "@/components/products/addproduct";
import seeproduct from "@/components/products/seeproduct";

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