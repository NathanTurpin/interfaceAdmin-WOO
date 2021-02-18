<template>
  <div>
    <section id="app" class="content">
      <div v-for="(product, idProduct) in products" :key="idProduct">
        <div class="col">
          <div class="card">
            <img
              v-if="product.images[0] !== undefined"
              :src="product.images[0].thumbnail"
              alt=""
              class="card-img-top"
            />
            <div class="card-body">
              <h5 class="card-title">{{ product.name }}</h5>
              <p class="card-text" v-html="product.description"></p>
              <p class="card-text" v-html="product.price_html"></p>

              <button @click="deleteProduct(product.id)">supp</button>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import axios from "axios";
import WooCommerceRestApi from "@woocommerce/woocommerce-rest-api";
export default {
  data() {
    return {
      products: [],
      token: localStorage.getItem("token"),
    };
  },
  mounted() {
    this.getProduct();
  },
  methods: {
    // AFFICHE LES PRODUITS
    getProduct() {
      axios
        .get("https://applicommande.local/wp-json/wc/store/products")
        .then((response) => (this.products = response.data))
        .catch((error) => console.log(error));
    },
    // DELETE PRODUCT
    deleteProduct(idP) {
      alert(idP);
      const url = "http://applicommande.local/wp-json/wc/v3/products/" + idP;
      axios
        .delete(url, {
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then(res => console.log(res))
        .catch((error) => {
          console.log(error);
        });
     
    },
  },
};
</script>

<style>
.content {
  display: flex;
  flex-wrap: wrap;
}
.card {
  width: 60%;
  min-height: 50vh;
}
</style>
