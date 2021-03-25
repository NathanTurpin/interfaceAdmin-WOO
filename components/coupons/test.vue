<template>
  <b-modal
    id="modal-scrollable modal-xl"
    scrollable
    size="xl"
    ref="modal1"
    :title="coupon.code"
  >
    <!-- // PRODUITS POUR LE COUPON -->
    <div>
      <label class="col-sm-2 col-form-label" for=""> Produits : </label>
      <div class="col-sm-5">
        <select
          v-model="selectedProdExclu"
          @change="getProductVariantExclu(selectedProdExclu)"
        >
          <option v-for="(product, key) in products" :value="key" :key="key">
            {{ product.name }}
          </option>
        </select>

        <div v-if="variantTestExclu">
          <div
            v-for="(proVar, key) in productsVariantsExclu"
            :value="key"
            :key="key"
          >
            <p v-for="nameProVar in proVar.attributes">
              <button @click="pushProdVariantExclu(proVar)">
                {{ nameProVar.name }} {{ nameProVar.option }}
              </button>
            </p>
          </div>
        </div>
      </div>
      <div class="col-sm-5">
        <label class="col-sm-4 col-form-label">Selectionnés:</label>

        <div
          v-for="(prod, index) in productCouponExclu"
          :key="index"
          @click="suppProCouponExclu(index, prod)"
        >
          {{ prod.name }} {{prod.id}}
          <p v-for="prodVar in prod.attributes">
            {{ prodVar.name }}
            {{ prodVar.option }}

          </p>
        </div>
      </div>
    </div>
    <br />
    <button @click="editCoupon()">Valider</button>
  </b-modal>
</template>

<script>
import axios from "axios";
export default {
  name: "test",
  props: ["coupon", "productCouponExclu"],
  data() {
    return {
      token: localStorage.getItem("token"),
      products: [],
      productsVariantsExclu: [],
      selectedProdExclu: "",
      variantTestExclu: false,
      lastTabProductsIDExclu: []
    };
  },
  mounted() {
    this.getProduct();
  },
  methods: {
    show() {
      this.$refs.modal1.show();
    },
    getProduct() {
      axios
        .get(window.addresse + "wp-json/wc/v3/products", {
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then((response) => (this.products = response.data))
        .catch((error) => console.log(error));

      // console.log(this.products.id)
    },
    async getProductVariantExclu(product) {
      console.log(this.products[product]);
      if (this.products[product].variations.length != 0) {
        this.variantTestExclu = true;
        this.productsVariantsExclu = [];
        for (let j = 0; j < this.products[product].variations.length; j++) {
          await axios
            .get(
              window.addresse +
                "/wp-json/wc/v3/products/" +
                this.products[product].id +
                "/variations/" +
                this.products[product].variations[j],
              {
                headers: {
                  Authorization: "Bearer " + this.token,
                },
              }
            )
            .then(
              (response) => this.productsVariantsExclu.push(response.data)
            )
            .catch((error) => console.log(error));
        }
      } else {
        this.variantTestExclu = false;
        this.productCouponExclu.push(this.products[product]);
      }
    },
    pushProdVariantExclu(productVar) {
      this.productCouponExclu.push(productVar);
    },
    suppProCouponExclu(index, prod) {
     
      this.productCouponExclu.splice(index, 1);
    },
    editCoupon(){
        console.log('product Coupon')
        this.lastTabProductsIDExclu = []
        for(let i =0;i<this.productCouponExclu.length;i++){
        this.lastTabProductsIDExclu.push(this.productCouponExclu[i].id)
        }
        console.log(this.lastTabProductsIDExclu)
    }
  },
};
</script>

<style>
</style>