<template>
  <div>
    <select v-model="selected" 
        @change="test(selected)"
     >
      <option
        v-for="(product, key) in products"
        :value="key"
        :key="key"
      >
        {{ product.name }}
      </option>
    </select>

    <div v-for="item in reletedId">
      {{ item }}
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      token: localStorage.getItem("token"),
      products: [],
      selected: "",
      reletedId: [],
    };
  },
  mounted() {
    this.getProduct();
  },
  methods: {
    getProduct() {
      axios
        .get("http://applicommande.local/wp-json/wc/v3/products", {
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then(
          (response) => (
            (this.products = response.data), console.log(this.products)
          )
        )
        .catch((error) => console.log(error));
    },
    test(item) {
      return this.reletedId.push(this.products[item].id);
    },
  },
};
</script>

<style>
</style>