<template>
  <div class="container">
    <div class="search-wrapper">
      <input type="text" v-model="search" placeholder="Search title.." />
      <label>Search title:</label>
    </div>
    <section id="app" class="content">
      <div v-for="(product, idProduct) in filteredList" :key="idProduct">
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
            Prix:
            <p v-if="product.salePrice">{{ product.salePrice }}</p>
            <p v-else v-html="product.price_html"></p>
            Stock:
            {{ product.stock_quantity }}
            variations :
            <div v-show="products_id === idProduct">
              <div
                v-for="(variation, idVar) in variations"
                v-b-modal.modal-1
                :key="idVar"
              >
                <div
                  v-for="varia in variation.attributes"
                  @click="seeProVar(variation, product)"
                >
                  {{ varia.name }}

                  {{ varia.option }}
                </div>
              </div>
            </div>
          </template>
          <h5>{{ product.name }}</h5>
          <b-card-text> <p v-html="product.description"></p> </b-card-text>
          <template #footer>
            <button @click="deleteProduct(product.id)">supp</button>
            <button @click="showEditComponent(product.id)">edit</button>
            <button
              v-if="product.variations[0]"
              to="../seeVariations"
              @click="seeProductVariations(product.id, idProduct)"
            >
              Voir les variables
            </button>
          </template>
        </b-card>
        <div v-show="showEdit">
          <editProduct :idEditProduct="idEditProduct" :product="product" />
        </div>
      </div>
    </section>
    <seeProductVariant
      v-show="componentProVariant"
      :product="product"
      :produit_variant="produit_variant"
    />
  </div>
</template>

<script>
import axios from "axios";
import seeProductVariant from "@/components/products/seeProductVariant";
import editProduct from "@/components/editProduct";
export default {
  components: {
    editProduct,
    seeProductVariant,
  },
  data() {
    return {
      search: "",
      products: [],
      token: localStorage.getItem("token"),
      showEdit: false,
      idEditProduct: "",
      variations: [],
      products_id: null,
      produit_variant: [],
      componentProVariant: false,
      product: [],
    };
  },
  mounted() {
    this.getProduct();
  },
  computed: {
    filteredList() {
      return this.products.filter((product) => {
        return product.name.toLowerCase().includes(this.search.toLowerCase());
      });
    },
  },
  methods: {
    // AFFICHE LES PRODUITS
    getProduct() {
      axios
        .get(window.addresse + "wp-json/wc/v3/products", {
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
    // DELETE PRODUCT
    deleteProduct(idP) {
      alert(idP);
      const url = window.addresse + "wp-json/wc/v3/products/" + idP;
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
    // SEE PRODUCTS VARIATIONS

    seeProductVariations(id, idProduct) {
      this.variations = "";
      this.products_id = idProduct;
      axios
        .get(
          window.addresse + "/wp-json/wc/v3/products/" + id + "/variations/",
          {
            headers: {
              Authorization: "Bearer " + this.token,
            },
          }
        )
        .then(
          (response) => (
            (this.variations = response.data),
            console.log(this.variations[idProduct])
          )
        )
        .catch((error) => console.log(error));
    },
    // OPEN COMPONENT SEEPRODUCUTVARIATIONS
    seeProVar(item, product) {
      this.produit_variant = item;
      this.componentProVariant = true;
      this.product = product;
    },
  },
};
</script>

<style scoped>
.card-img img {
  max-width: 300px;
  max-height: 300px;
}
</style>
