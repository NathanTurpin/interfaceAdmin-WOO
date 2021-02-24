<template>
  <div id="app">
    <div
      v-if="!registerActive"
    >
      <a href="#" @click="registerActive = !registerActive">
        Ajouter un produit</a
      >
      <test />
    </div>

    <div v-else >
      <a href="#" @click="registerActive = !registerActive"
        >Voir les produits</a
      >
      <test1 />
    </div>
  </div>
</template>

<script>
import test from "@/components/test/test";
import test1 from "@/components/test/test1";

export default {
  components: {
    test,
    test1,
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