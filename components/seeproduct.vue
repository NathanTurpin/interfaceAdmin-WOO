<template>
  <div class="container">
    <section id="app" class="content">
      <div v-for="(product, idProduct) in products" :key="idProduct">
        <b-card
          header-tag="header"
          class="card-img mb-3"
          footer-tag="footer"
          v-if="product.images[0] !== undefined"
          :img-src="product.images[0].src"
          img-alt="Card image"
          img-left
          img-fluid
        >
          <template #header>
            <p v-html="product.price_html"></p>
            {{product.stock_quantity}}
          </template>
          <h5>{{ product.name }}</h5>
          <b-card-text> <p v-html="product.description"></p> </b-card-text>
          <template #footer>
            <button @click="deleteProduct(product.id)">supp</button>
            <button @click="showEditComponent(product.id)">edit</button>
            
          </template>
        </b-card><div v-show="showEdit">
              <editProduct :idEditProduct="idEditProduct" :product="product"/>
            </div>
      </div>
    </section>
  </div>
</template>

<script>
import axios from "axios";
import editProduct from "@/components/editProduct";
export default {
  components: {
    editProduct,
  },
  data() {
    return {
      products: [],
      token: localStorage.getItem("token"),
      showEdit: false,
      idEditProduct: "",
    };
  },
  mounted() {
    this.getProduct();
  },
  methods: {
    // AFFICHE LES PRODUITS
    getProduct() {
      axios
        .get("http://applicommande.local/wp-json/wc/v3/products",{
          headers: {
            Authorization: "Bearer " + this.token
          }})
        .then((response) => (this.products = response.data,console.log( this.products)))
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
        .then((res) => console.log(res))
        .catch((error) => {
          console.log(error);
        });
    },
    // EDIT PRODUCT
    showEditComponent(id) {
      this.showEdit = !this.showEdit;
      this.idEditProduct = id;
    },
  },
};
</script>

<style scoped>

.card-img img{
  max-width: 300px;
  max-height: 300px;
}
</style>
