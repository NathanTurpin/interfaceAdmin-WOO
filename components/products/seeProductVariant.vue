<template>
  <div>
    <b-modal id="modal-1" :title="product.name">
      <h5>
        {{ product.id }}
      </h5>
      <input type="number" v-model="priceEdit">
      <p class="my-4">{{ produit_variant }}</p>
      <button @click="editProVariant">edit</button>
    </b-modal>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: "seeProductVariant",
  props: ["produit_variant", "product"],
  data() {
    return {
      token: localStorage.getItem("token"),
      priceEdit: this.produit_variant.price
    };
  },
  methods: {
    editProVariant() {
      alert(this.product.id);
      alert(this.produit_variant.id);
      const url =
        window.addresse +
        "/wp-json/wc/v3/products/" +
        this.product.id +
        "/variations/" +
        this.produit_variant.id;
      axios
        .put(
          url,
          {
            regular_price: this.priceEdit,
          },
          {
            headers: {
              Authorization: "Bearer " + this.token,
            },
          }
        )
        .then((res) => console.log(res))
        .catch((error) => {
          console.log(error);
        });
    },
  },
};
</script>

<style>
</style>