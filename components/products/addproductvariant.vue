<template>
  <div>
    <!-- ATTRIBUTE -->
    <section class="attributes"></section>

    <!-- TERMES -->
    <section class="termes"></section>

    <!-- PRODUITS VARIANTS -->
    <section class="variants">
      <button @click="addVariants()">add variant</button>
    </section>
  </div>
</template>

<script>
import axios from "axios";
export default {
  props: ["products", "newProductId"],
  data() {
    return {
      token: localStorage.getItem("token"),
    };
  },
  mounted() {},
  methods: {
    addVariants() {
      axios
        .post(
          "http://applicommande.local/wp-json/wc/v3/products/" +
            this.newProductId +
            "/variations",
          {
            regular_price: "15.00",
            attributes: [
              {
                id: 2,
                options: "xxl",
              },
            ],
          },
          {
            headers: {
              Authorization: "Bearer " + this.token,
            },
          }
        )
        .then((response) => console.log(response))
        .catch((error) => console.log(error));
    },
  },
};
</script>

<style>
</style>