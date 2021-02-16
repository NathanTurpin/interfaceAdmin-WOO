<template>
  <div >
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
              
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      products: [],
      token: localStorage.getItem('token'),
      
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
