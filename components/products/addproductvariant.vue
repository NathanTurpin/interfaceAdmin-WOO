<template>
  <div>
    <!-- ATTRIBUTE -->
    <section class="attributes"></section>

    <!-- TERMES -->
    <section class="termes">
      <div v-for="(terme,idTerme) in termes" :key="idTerme">
      {{terme.name}} <button @click="addVariants(idTerme)">add variant</button>
      <input type="number" v-model="termes[idTerme].regularPrice">
      </div>
    </section>

    <!-- PRODUITS VARIANTS -->
    <section class="variants">
      
    </section>
  </div>
</template>

<script>
import axios from "axios";
export default {
  props: ["products", "newProductId", "termes", "attributeID"],
  data() {
    return {
      token: localStorage.getItem("token"),
    
    };
  },
  mounted() {},
  methods: {
    addVariants(idTerme) {
      console.log(this.termes[idTerme].name)
      console.log(this.attributeID.name)
      // axios
      //   .post(
      //     "http://applicommande.local/wp-json/wc/v3/products/" +
      //       this.newProductId +
      //       "/variations",
      //     {
      //       regular_price: this.termes[idTerme].regularPrice,
      //       attributes: [
      //         {
      //           id: this.termes[idTerme].id,
      //           option: this.termes[idTerme].name,
      //         },
      //       ],
      //     },
      //     {
      //       headers: {
      //         Authorization: "Bearer " + this.token,
      //       },
      //     }
      //   )
      //   .then((response) => console.log(response))
      //   .catch((error) => console.log(error));
     },
  },
};
</script>

<style>
</style>