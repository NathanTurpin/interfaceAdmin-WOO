<template>
  <div class="container">
    <section class="code">
      <!-- NAME -->
      <b-form-input
        id="input-1"
        v-model="form.code"
        type="text"
        placeholder="Code promo"
        required
      >
      </b-form-input>
      <button type="button" class="btn btn-info" @click="generateRdmCode()">Generate</button>

      <!-- DESCRIPTION -->

      <b-form-input
        id="input-2"
        v-model="form.description"
        placeholder="Entrer la description"
        required
      ></b-form-input>
    </section>
    <section class="general">
      <div>
        <b-card no-body class="overflow-hidden" style="max-width: 540px">
          <b-row no-gutters>
            <b-col md="3" class="btnGeneral">
              <b-button @click="generalBtn()" variant="dark">Général</b-button>
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
                      <select v-model="form.discount_type">
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
                      <input type="number" v-model="form.amount" /> <br />
                    </div>
                    <label for="" class="col-sm-4 col-form-label">
                      Autoriser l'expédition gratuite :
                    </label>
                    <div class="col-sm-8"><input type="checkbox" /> <br /></div>
                    <label for="" class="col-sm-4 col-form-label">
                      Date d'expiration du code :
                    </label>
                    <div class="col-sm-8">
                      <input type="date" v-model="form.date_expires" />
                    </div>
                  </div>
                </b-card-text>

                <b-card-text v-show="restriBtn">
                  <div class="form-group row">
                    <label class="col-sm-4 col-form-label" for="">
                      Dépense minimal :
                    </label>
                    <div class="col-sm-8">
                      <input type="number" v-model="form.minimum_amount" />
                      <br />
                    </div>
                    <label class="col-sm-4 col-form-label" for="">
                      Dépense maximum :
                    </label>
                    <div class="col-sm-8">
                      <input type="number" v-model="form.maximum_amount" />
                      <br />
                    </div>
                    <label class="col-sm-4 col-form-label" for="">
                      Utilisation individuelle uniquement :
                    </label>
                    <div class="col-sm-8">
                      <input type="checkbox" v-model="form.individual_use" />
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
                        v-model="form.exclude_sale_items"
                      />
                    </div>
                    <br />
                    <hr />

                    <!-- // PRODUITS POUR LE COUPON -->
                    <label class="col-sm-4 col-form-label" for="">
                      Produits :
                    </label>
                    <div class="col-sm-8">
                      <select
                        v-model="selectedProd"
                        @change="pushProd(selectedProd)"
                      >
                        <option
                          v-for="(product, key) in products"
                          :value="key"
                          :key="key"
                        >
                          {{ product.name }}
                        </option>
                      </select>
                    </div>
                    <br />
                    <p>Selectionnés:</p>
                    <span v-for="prod in tabProdName"> {{ prod }}</span> <br />

                    <!-- PRODUITS EXCLU DU COUPON -->
                    <label class="col-sm-4 col-form-label" for="">
                      Exclure les produits :
                    </label>
                    <div class="col-sm-8">
                      <select
                        v-model="selectedProdExclu"
                        @change="pushExcluProd(selectedProdExclu)"
                      >
                        <option
                          v-for="(product, key) in products"
                          :value="key"
                          :key="key"
                        >
                          {{ product.name }}
                        </option>
                      </select>
                    </div>
                    <br />
                    <p>Selectionnés:</p>
                    <span v-for="prod in tabProdExcluName"> {{ prod }}</span>
                    <br /><br />
                    <hr />

                    <!-- CATEGORIES POUR LE COUPON -->
                    <label class="col-sm-4 col-form-label" for="">
                      Categories de produits :
                    </label>
                    <div class="col-sm-8">
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
                    <br />
                    <p>Selectionnés:</p>
                    <span v-for="cat in tabCatName"> {{ cat }}</span> <br />

                    <br />

                    <!-- CATEGORIES EXCLU DU COUPON -->
                    <label class="col-sm-4 col-form-label" for="">
                      Exclure les categories :
                    </label>
                    <div class="col-sm-8">
                      <select
                        v-model="selectedCatExclu"
                        @change="pushCatExclu(selectedCatExclu)"
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
                    <br />
                    <p>Selectionnés:</p>
                    <span v-for="cat in tabCatExcluName"> {{ cat }}</span>
                    <br />
                  </div>
                </b-card-text>

                <b-card-text v-show="limiteBtn">
                  <div class="form-group row">
                    <label class="col-sm-4 col-form-label" for="">
                      Limite d’utilisation par code :
                    </label>
                    <div class="col-sm-8">
                      <input type="number" v-model="form.usage_limit" /><br />
                    </div>
                    <label class="col-sm-4 col-form-label" for="">
                      Limite d’utilisation par utilisateur:
                    </label>
                    <div class="col-sm-8">
                      <input
                        type="number"
                        v-model="form.usage_limit_per_user"
                      />
                    </div>
                    <br />
                    <label class="col-sm-4 col-form-label" for="">
                      Max number of items in the cart the coupon can be applied
                      to :
                    </label>
                    <div class="col-sm-8">
                      <input
                        type="number"
                        v-model="form.limit_usage_to_x_items"
                      />
                    </div>
                  </div>
                </b-card-text>
              </b-card-body>
            </b-col>
          </b-row>
        </b-card>
      </div>
      <button type="button" class="btn btn-danger btnValider" @click="addCoupon()">Valider</button>
    </section>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      token: localStorage.getItem("token"),
      geneBtn: true,
      restriBtn: false,
      limiteBtn: false,
      selected: "fixed_cart",
      selectedProd: "",
      selectedProdExclu: "",
      selectedCat: "",
      selectedCatExclu: "",
      products: [],
      categories: [],
      tabProdName: [],
      tabProdExcluName: [],
      tabCatName: [],
      tabCatExcluName: [],
      options: [
        { text: "Panier", value: "fixed_cart" },
        { text: "pourcentage", value: "percent" },
        { text: "Produit", value: "fixed_product" },
      ],
      form: {
        code: "",
        description: "",
        discount_type: "fixed_cart",
        amount: null,
        date_expires: "",
        maximum_amount: null,
        minimum_amount: null,
        individual_use: false,
        exclude_sale_items: false,
        tabProd: [],
        tabExcluProd: [],
        tabCat: [],
        tabCatExclu: [],
        usage_limit: null,
        usage_limit_per_user: null,
        limit_usage_to_x_items: null,
      },
    };
  },
  mounted() {
    this.getProduct();
    this.getCategories();
  },
  methods: {
    addCoupon() {
      axios
        .post(
          window.addresse + "wp-json/wc/v3/coupons",
          {
            code: this.form.code,
            description: this.form.description,
            discount_type: this.form.discount_type,
            amount: this.form.amount,
            date_expires: this.form.date_expires,
            maximum_amount: this.form.maximum_amount,
            minimum_amount: this.form.minimum_amount,
            individual_use: this.formindividual_use,
            exclude_sale_items: this.form.exclude_sale_items,
            product_ids: this.form.tabProd,
            excluded_product_ids: this.form.tabExcluProd,
            product_categories: this.form.tabCat,
            excluded_product_categories: this.form.tabCatExclu,
            usage_limit: this.form.usage_limit,
            usage_limit_per_user: this.form.usage_limit_per_user,
            limit_usage_to_x_items: this.form.limit_usage_to_x_items,
          },
          { headers: { Authorization: "Bearer " + this.token } }
        )
        .then((response) => console.log(response));
    },
    pushProd(selectedProd) {
      return (
        this.form.tabProd.push(this.products[selectedProd].id),
        this.tabProdName.push(this.products[selectedProd].name)
      );
    },
    pushExcluProd(selectedProdExclu) {
      return (
        this.form.tabExcluProd.push(this.products[selectedProdExclu].id),
        this.tabProdExcluName.push(this.products[selectedProdExclu].name)
      );
    },
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
    getCategories() {
      axios
        .get(window.addresse + "wp-json/wc/v3/products/categories", {
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then(
          (response) => (
            (this.categories = response.data), console.log(this.categories)
          )
        )
        .catch((error) => console.log(error));
    },
    pushCat(selectedCat) {
      return (
        this.form.tabCat.push(this.categories[selectedCat].id),
        this.tabCatName.push(this.categories[selectedCat].name)
      );
    },
    pushCatExclu(selectedCatExclu) {
      return (
        this.form.tabCatExclu.push(this.categories[selectedCatExclu].id),
        this.tabCatExcluName.push(this.categories[selectedCatExclu].name)
      );
    },
    generateRdmCode() {
      var result = "";
      var characters =
        "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
      var charactersLength = characters.length;
      for (var i = 0; i < 6; i++) {
        result += characters.charAt(
          Math.floor(Math.random() * charactersLength)
        );
      }
      this.form.code = result;
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
    margin : 1% 0% 1% 0%
}
</style>