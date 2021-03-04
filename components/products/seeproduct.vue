<template>
  <div class="container-fluid">
    <div class="search-wrapper">
      <label>Search title:</label>

      <input type="text" v-model="search" placeholder="Search title.." />
    </div>
    <div class="card-group">
      <div
        class="card"
        v-for="(product, idProduct) in filteredList"
        :key="idProduct"
      >
        <img
          class="card-img-top img-fluid"
          v-if="product.images[0] !== undefined"
          :src="product.images[0].src"
          alt="Card image cap"
        />
        <div class="card-body">
          <h5 class="card-title">
            <h3>{{ product.name }}</h3>
          </h5>
          <p class="card-text" v-html="product.description"></p>
          <div v-if="product.salePrice" class="pricing">
            <p>{{ product.salePrice }}</p>
          </div>
          <p v-else v-html="product.price_html"></p>
          Stock:
          {{ product.stock_quantity }} <br />

          <button
            v-if="product.variations[0]"
            @click="seeProductVariations(product.id, idProduct)"
            class="btn btn-dark"
          >
            Voir les variables
          </button>
          <div v-show="products_id === idProduct">
            <div
              v-for="(variation, idVar) in variations"
              v-b-modal.modal-1
              :key="idVar"
            >
              <button
                v-for="(varia, idvaria) in variation.attributes"
                :key="idvaria"
                @click="seeProVar(variation, product)"
                class="btn btn-light"
              >
                {{ varia.name }}

                {{ varia.option }}
              </button>
            </div>
          </div>
          <br /><br />
          <button class="btn btn-danger" @click="deleteProduct(product.id)">
            supp
          </button>
          <button class="btn btn-info" @click="showEditComponent(product.id,idProduct)">
            edit
          </button>

          <div v-show="showEdit && idTab === idProduct">
            <editProduct :idEditProduct="idEditProduct" :product="product" />
          </div>
        </div>
      </div>
    </div>

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
      idTab:null
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
    // filteredList() {
    //   return this.products.filter((product) => {
    //   return this.makeArray(this.products).filter((product) => {
    //     return product.name.toLowerCase().includes(this.search.toLowerCase());
    //   });
    // })},
    // makeArray(any) {
    //   return Array.isArray(any) ? any : [];
    // }
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
    showEditComponent(id,idProduct) {
      this.showEdit = !this.showEdit;
      this.idEditProduct = id;
      this.idTab = idProduct
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
.card {
  margin: 1%;
  max-width: 100%;
}

.btn-light {
  margin: 1%;
}
</style>
