<template>
  <div class="container">
    <b-modal
      id="modal-scrollable modal-xl"
      scrollable
      size="xl"
      ref="modal1"
      :title="coupon.code"
    >
      <input type="number" v-model="coupon.amount" />
      {{ coupon }}
      <section class="code">
        <!-- DESCRIPTION -->
        <b-form-input
          id="input-2"
          v-model="coupon.description"
          placeholder="Entrer la description"
          required
        ></b-form-input>
      </section>
      <section class="general">
        <div>
          <b-card no-body class="overflow-hidden" style="max-width: 540px">
            <b-row no-gutters>
              <b-col md="3" class="btnGeneral">
                <b-button @click="generalBtn()" variant="dark"
                  >Général</b-button
                >
                <b-button @click="restrictionBtn()" variant="dark"
                  >Restriction d'usage</b-button
                >
                <b-button @click="limitBtn()" variant="dark"
                  >Limite d'utilisation</b-button
                >
              </b-col>
              <b-col md="9">
                <b-card-body title="">
                  <b-card-text class="test" v-show="geneBtn">
                    <div class="form-group row">
                      <label for="" class="col-sm-4 col-form-label"
                        >Type de remise :</label
                      >
                      <div class="col-sm-8">
                        <select v-model="coupon.discount_type">
                          <option
                            v-for="option in options"
                            v-bind:value="option.value"
                          >
                            {{ option.text }}
                          </option>
                        </select>
                      </div>

                      <br />
                      <label for="" class="col-sm-4 col-form-label">
                        Valeur du code promo :
                      </label>
                      <div class="col-sm-8">
                        <input type="number" v-model="coupon.amount" /> <br />
                      </div>
                      <label for="" class="col-sm-4 col-form-label">
                        Autoriser l'expédition gratuite :
                      </label>
                      <div class="col-sm-8">
                        <input type="checkbox" /> <br />
                      </div>
                      <label for="" class="col-sm-4 col-form-label">
                        Date d'expiration du code :
                      </label>

                      <div class="col-sm-8">
                        {{ coupon.date_expires }}

                        <button @click="changeDateBtn = !changeDateBtn">
                          Changer la date
                        </button>
                        <input
                          v-show="changeDateBtn"
                          type="date"
                          v-model="coupon.date_expires"
                        />
                      </div>
                    </div>
                  </b-card-text>

                  <b-card-text v-show="restriBtn">
                    <div class="coupon-group row">
                      <label class="col-sm-4 col-form-label" for="">
                        Dépense minimal :
                      </label>
                      <div class="col-sm-8">
                        <input type="number" v-model="coupon.minimum_amount" />
                        <br />
                      </div>
                      <label class="col-sm-4 col-form-label" for="">
                        Dépense maximum :
                      </label>
                      <div class="col-sm-8">
                        <input type="number" v-model="coupon.maximum_amount" />
                        <br />
                      </div>
                      <label class="col-sm-4 col-form-label" for="">
                        Utilisation individuelle uniquement :
                      </label>
                      <div class="col-sm-8">
                        <input
                          type="checkbox"
                          v-model="coupon.individual_use"
                        />
                        (Cochez cette case si le code promo ne peut être utilisé
                        conjointement avec d’autres codes promo.)
                      </div>
                      <br />
                      <label class="col-sm-4 col-form-label" for="">
                        Exclure les articles en promo :
                      </label>
                      <div class="col-sm-8">
                        <input
                          type="checkbox"
                          v-model="coupon.exclude_sale_items"
                        />
                      </div>
                      <br />
                      <hr />

                      <!-- // PRODUITS POUR LE COUPON -->
                      <div class="produits">
                        <label class="col-sm-2 col-form-label" for="">
                          Produits :
                        </label>
                        <div class="col-sm-3">
                          <select
                            v-model="selectedProd"
                            @change="getProductVariant(selectedProd)"
                          >
                            <option
                              v-for="(product, key) in products"
                              :value="key"
                              :key="key"
                            >
                              {{ product.name }}
                            </option>
                          </select>

                          <div v-if="variantTest">
                            <div
                              v-for="(proVar, key) in productsVariants"
                              :value="key"
                              :key="key"
                            >
                              <p v-for="nameProVar in proVar.attributes">
                                <button @click="pushProdVariant(proVar)">
                                  {{ nameProVar.name }} {{ nameProVar.option }}
                                </button>
                              </p>
                            </div>
                          </div>
                        </div>
                        <div class="col-sm-7">
                          <label>Selectionnés:</label>
                          <div id="selectedProducts">
                            <div
                              v-for="(prod, index) in productCoupon"
                              :key="index"
                              @click="suppProCoupon(index, prod)"
                              id="selectedProEspace"
                            >
                              {{ prod.name }}
                              <span v-for="prodVar in prod.attributes">
                                {{ prodVar.name }} {{ prodVar.option }}
                              </span>
                            </div>
                          </div>
                        </div>
                      </div>
                      <br />

                      <!-- PRODUITS EXCLU DU COUPON -->
                      <div class="produits">
                        <label class="col-sm-2 col-form-label" for="">
                          Produits exclu :
                        </label>
                        <div class="col-sm-3">
                          <select
                            v-model="selectedProdExclu"
                            @change="getProductVariantExclu(selectedProdExclu)"
                          >
                            <option
                              v-for="(product, key) in products"
                              :value="key"
                              :key="key"
                            >
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
                                  {{ nameProVar.name }}
                                  {{ nameProVar.option }}
                                </button>
                              </p>
                            </div>
                          </div>
                        </div>
                        <div class="col-sm-7">
                          <label>Selectionnés:</label>
                          <div id="selectedProducts">
                            <div
                              v-for="(prod, index) in productCouponExclu"
                              :key="index"
                              @click="suppProCouponExclu(index, prod)"
                              id="selectedProEspace"
                            >
                              {{ prod.name }}
                              <span v-for="prodVar in prod.attributes">
                                {{ prodVar.name }}
                                {{ prodVar.option }}
                              </span>
                            </div>
                          </div>
                        </div>
                      </div>

                      <hr />

                      <!-- // Categories POUR LE COUPON -->
                      <div class="produits">
                        <label class="col-sm-2 col-form-label" for="">
                          Categories :
                        </label>
                        <div class="col-sm-3">
                          <select
                            v-model="selectedCat"
                            @change="pushCat(selectedCat)"
                          >
                            <option
                              v-for="(cat, key) in categories"
                              :value="key"
                              :key="key"
                            >
                              {{ cat.name }}
                            </option>
                          </select>
                        </div>
                        <div class="col-sm-7">
                          <label>Selectionnés:</label>

                          <div id="selectedProducts">
                            <span
                              v-for="(cat, index) in categorieCoupon"
                              :key="index"
                              @click="suppCatCoupon(index)"
                              id="selectedProEspace"
                            >
                              {{ cat.name }}</span
                            >
                          </div>
                        </div>
                      </div>
                      <br />
                      <br />

                      <!-- // categories exclu POUR LE COUPON -->
                      <div class="produits">
                        <label class="col-sm-2 col-form-label" for="">
                          Categories exclu:
                        </label>
                        <div class="col-sm-3">
                          <select
                            v-model="selectedCatExclu"
                            @change="pushCatExclu(selectedCatExclu)"
                          >
                            <option
                              v-for="(cate, key) in categories"
                              :value="key"
                              :key="key"
                            >
                              {{ cate.name }}
                            </option>
                          </select>
                        </div>
                        <br />
                        <div class="col-sm-7">
                          <label>Selectionnés:</label>

                          <div id="selectedProducts">
                            <span
                              v-for="(cate, i) in categorieCouponExclu"
                              :key="i"
                              @click="suppCatCouponExclu(i)"
                              id="selectedProEspace"
                            >
                              {{ cate.name }}</span
                            >
                          </div>
                        </div>
                      </div>
                    </div>
                  </b-card-text>

                  <b-card-text v-show="limiteBtn">
                    <div class="coupon-group row">
                      <label class="col-sm-4 col-form-label" for="">
                        Limite d’utilisation par code :
                      </label>
                      <div class="col-sm-8">
                        <input
                          type="number"
                          v-model="coupon.usage_limit"
                        /><br />
                      </div>
                      <label class="col-sm-4 col-form-label" for="">
                        Limite d’utilisation par utilisateur:
                      </label>
                      <div class="col-sm-8">
                        <input
                          type="number"
                          v-model="coupon.usage_limit_per_user"
                        />
                      </div>
                      <br />
                      <label class="col-sm-4 col-form-label" for="">
                        Max number of items in the cart the coupon can be
                        applied to :
                      </label>
                      <div class="col-sm-8">
                        <input
                          type="number"
                          v-model="coupon.limit_usage_to_x_items"
                        />
                      </div>
                    </div>
                  </b-card-text>
                </b-card-body>
              </b-col>
            </b-row>
          </b-card>
        </div>
        <button
          type="button"
          class="btn btn-danger btnValider"
          @click="editCoupon(coupon.id)"
        >
          Valider
        </button>
      </section>
    </b-modal>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "editCoupon",
  props: [
    "idEditCoupon",
    "coupon",
    "productCoupon",
    "productCouponExclu",
    "categories",
    "categorieCoupon",
    "categorieCouponExclu",
    "products",
  ],
  data() {
    return {
      token: localStorage.getItem("token"),
      changeDateBtn: false,
      geneBtn: true,
      restriBtn: false,
      limiteBtn: false,
      selected: "fixed_cart",
      selectedProd: "",
      selectedProdExclu: "",
      selectedCat: "",
      selectedCatExclu: "",

      lastTabProductsID: [],
      lastTabProductsIDExclu: [],
      lastTabCatID: [],
      lastTabCatExcluID: [],

      productsVariants: [],
      productsVariantsExclu: [],
      variantTest: false,
      variantTestExclu: false,

      options: [
        { text: "Panier", value: "fixed_cart" },
        { text: "pourcentage", value: "percent" },
        { text: "Produit", value: "fixed_product" },
      ],

      coupon: {
        id: this.coupon.id,
        description: this.coupon.description,
        discount_type: this.coupon.discount_type,
        amount: this.coupon.amount,
        date_expires: this.coupon.date_expires,
        maximum_amount: this.coupon.maximum_amount,
        minimum_amount: this.coupon.minimum_amount,
        individual_use: this.coupon.individual_use,
        exclude_sale_items: this.coupon.exclude_sale_items,
        tabCat: [this.coupon.tabCat],
        tabCatExclu: [this.coupon.tabCatExclu],
        usage_limit: this.coupon.usage_limit,
        usage_limit_per_user: this.coupon.usage_limit_per_user,
        limit_usage_to_x_items: this.coupon.limit_usage_to_x_items,
      },
    };
  },
  mounted() {},
  methods: {
    show() {
      this.$refs.modal1.show();
    },
    editCoupon(idCoupon) {
      alert(idCoupon);
      const url = window.addresse + "wp-json/wc/v3/coupons/" + idCoupon;
      console.log("product Coupon");
      for (let i = 0; i < this.productCoupon.length; i++) {
        this.lastTabProductsID.push(this.productCoupon[i].id);
      }
      console.log(this.lastTabProductsID);

      console.log("product exclu Coupon");
      for (let i = 0; i < this.productCouponExclu.length; i++) {
        this.lastTabProductsIDExclu.push(this.productCouponExclu[i].id);
      }
      console.log(this.lastTabProductsIDExclu);
      console.log("cat Coupon");
      this.lastTabCatID = [];
      this.lastTabCatExcluID = [];
      for (let i = 0; i < this.categorieCoupon.length; i++) {
        this.lastTabCatID.push(this.categorieCoupon[i].id);
      }
      console.log(this.lastTabCatID);
      console.log("cat exclu Coupon");

      for (let i = 0; i < this.categorieCouponExclu.length; i++) {
        this.lastTabCatExcluID.push(this.categorieCouponExclu[i].id);
      }
      console.log(this.lastTabCatExcluID);
      axios
        .put(
          url,
          {
            description: this.coupon.description,
            discount_type: this.coupon.discount_type,
            amount: this.coupon.amount,
            date_expires: this.coupon.date_expires,
            maximum_amount: this.coupon.maximum_amount,
            minimum_amount: this.coupon.minimum_amount,
            individual_use: this.coupon.individual_use,
            exclude_sale_items: this.coupon.exclude_sale_items,
            product_ids: this.lastTabProductsID,
            excluded_product_ids: this.lastTabProductsIDExclu,
            product_categories: this.lastTabCatID,

            excluded_product_categories: this.lastTabCatExcluID,
            usage_limit: this.coupon.usage_limit,
            usage_limit_per_user: this.coupon.usage_limit_per_user,
            limit_usage_to_x_items: this.coupon.limit_usage_to_x_items,
          },
          { headers: { Authorization: "Bearer " + this.token } }
        )
        .then(
          (response) => console.log(response),
          this.$emit("clicked", this.validate),
          (this.restriBtn = false),
          (this.limiteBtn = false),
          (this.geneBtn = true)
        )
        .catch((error) => {
          console.log(error);
        });
    },
    async getProductVariant(product) {
      console.log(this.products[product]);
      if (this.products[product].variations.length != 0) {
        this.variantTest = true;
        this.productsVariants = [];
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
            .then((response) => this.productsVariants.push(response.data))
            .catch((error) => console.log(error));
        }
      } else {
        this.variantTest = false;
        this.productCoupon.push(this.products[product]);
      }
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
            .then((response) => this.productsVariantsExclu.push(response.data))
            .catch((error) => console.log(error));
        }
      } else {
        this.variantTestExclu = false;
        this.productCouponExclu.push(this.products[product]);
      }
    },
    pushProdVariant(productVar) {
      this.productCoupon.push(productVar);
    },
    pushProdVariantExclu(productVar) {
      this.productCouponExclu.push(productVar);
    },
    suppProCoupon(index, prod) {
      this.productCoupon.splice(index, 1);
    },
    suppProCouponExclu(index, prod) {
      this.productCouponExclu.splice(index, 1);
    },
    pushCat(selectedCat) {
      this.categorieCoupon.push(this.categories[selectedCat]);
      console.log(this.categorieCoupon);
    },
    suppCatCoupon(index) {
      this.categorieCoupon.splice(index, 1);
    },
    pushCatExclu(selectedCatExclu) {
      this.categorieCouponExclu.push(this.categories[selectedCatExclu]);
      console.log(this.categorieCouponExclu);
    },
    suppCatCouponExclu(i) {
      this.categorieCouponExclu.splice(i, 1);
    },
    generalBtn() {
      (this.limiteBtn = false), (this.geneBtn = true), (this.restriBtn = false);
    },
    restrictionBtn() {
      (this.limiteBtn = false), (this.geneBtn = false), (this.restriBtn = true);
    },
    limitBtn() {
      (this.limiteBtn = true), (this.geneBtn = false), (this.restriBtn = false);
    },
  },
};
</script>

<style scoped>
.btnValider {
  float: right;
}
button {
  margin: 1% 0% 1% 0%;
}
.selectProdCat select {
  margin-top: 1%;
}
.selectProdCat {
  border: solid black 0.5px;
  margin: 1%;
  -webkit-border-radius: 10px 10px 10px 10px;
  border-radius: 10px 10px 10px 10px;
}
.selectProdCat div p {
  display: inline-block;
}
.produits {
  display: flex;
  flex-wrap: wrap;
}
#selectedProducts {
  display: flex;
  flex-wrap: wrap;
}
#selectedProEspace {
  margin: 1%;
  border: rgb(94, 94, 94) solid;
  padding: 1%;
  -webkit-border-radius: 10px 10px 10px 10px;
  border-radius: 10px 10px 10px 10px;
  transition: transform 0.2s;
}
#selectedProEspace:hover {
  background: rgb(179, 178, 178);
  border: black solid;
  cursor: not-allowed;
  transform: scale(1.1);
}
</style>