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
      <button @click="generateRdmCode()">Generate</button>

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
                  Type de remise :
                  <select v-model="form.discount_type">
                    <option
                      v-for="option in options"
                      v-bind:value="option.value"
                    >
                      {{ option.text }}
                    </option>
                  </select>

                  <br />
                  Valeur du code promo :
                  <input type="number" v-model="form.amount" /> <br />
                  Autoriser l'expédition gratuite :
                  <input type="checkbox" /> <br />
                  Date d'expiration du code :
                  <input type="date" v-model="form.date_expires" />
                </b-card-text>

                <b-card-text v-show="restriBtn">
                  Dépense minimal :
                  <input type="number" v-model="form.minimum_amount"/> <br />
                  Dépense maximum :
                  <input type="number" v-model="form.maximum_amount"/> <br />
                  Utilisation individuelle uniquement :
                  <input type="checkbox" v-model="form.individual_use" /> (Cochez cette case si le code promo
                  ne peut être utilisé conjointement avec d’autres codes promo.)
                  <br />
                  Exclure les articles en promo :
                  <input type="checkbox" v-model="form.exclude_sale_items" /> <br />
                  <hr />
                  Produits : <input type="text" /> <br />
                  Exclure les produits :
                  <input type="text" /><br />
                  <hr />
                  Categories de produits :<input type="text" /><br />
                  Exclure les categories :<input type="text" />
                </b-card-text>

                <b-card-text v-show="limiteBtn">
                  Limite d’utilisation par code :
                  <input type="number" /><br />
                  Limite d’utilisation par utilisateur:
                  <input type="number" />
                </b-card-text>
              </b-card-body>
            </b-col>
          </b-row>
        </b-card>
      </div>
      <button @click="addCoupon()">OK</button>
    </section>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      token: localStorage.getItem("token"),
      geneBtn: true,
      restriBtn: false,
      limiteBtn: false,
      selected: "fixed_cart",
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
        date_expires:"",
        maximum_amount:null,
        minimum_amount: null,
        individual_use:false,
        exclude_sale_items: false
      },
    };
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
            maximum_amount : this.form.maximum_amount,
            minimum_amount: this.form.minimum_amount,
            individual_use: this.formindividual_use,
            exclude_sale_items : this.form.exclude_sale_items
            
          },
          { headers: { Authorization: "Bearer " + this.token } }
        )
        .then(
          (response) => (
            console.log(response)
          )
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

<style>
</style>