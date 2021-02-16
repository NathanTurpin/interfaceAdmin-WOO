<template>
  <div id="app">
    <div
      v-if="!registerActive"
      class="card login"
      v-bind:class="{ error: emptyFields }"
    > <a
          href="#"
          @click="(registerActive = !registerActive)"
          >
          Ajouter un produit</a
        >
      <produits />

      

      
     
    </div>

    <div v-else class="card register" v-bind:class="{ error: emptyFields }">
          <a
          href="#"
          @click="(registerActive = !registerActive)"
          >Voir les produits</a
        >
      <addproduct />
    </div>
  </div>
</template>

<script>
import axios from "axios";
import addproduct from '@/components/addproduct'
import produits from '@/components/produits'

export default {
    components: {
        addproduct,
        produits
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

<style scoped>
</style>