<template>
  <div id="app">
    <div
      v-if="!registerActive"
    >
      <a href="#" @click="registerActive = !registerActive">
        Ajouter une catégorie</a
      >
      <seecategorie />
    </div>

    <div v-else >
      <a href="#" @click="registerActive = !registerActive"
        >Voir les catégories</a
      >
      <addcategorie />
    </div>
  </div>
</template>

<script>
import addcategorie from "@/components/addcategorie";
import seecategorie from "@/components/seecategorie";

export default {
  components: {
    addcategorie,
    seecategorie,
  },
  data() {
    return {
      registerActive: false,

    };
  },
  mounted() {},
  methods: {},
};
</script>

<style>
</style>