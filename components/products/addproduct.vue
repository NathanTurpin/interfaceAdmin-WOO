<template>
  <div class="container">
    <section class="typeProduct">
      <div>
        <b-form-select
          v-model="selectedProduct"
          :options="options"
        ></b-form-select>
        <div class="mt-3">
          Type de produit : <strong>{{ selectedProduct }}</strong>
        </div>
      </div>
    </section>

    <section class="produit">
      <!-- NAME -->
      <b-form-group
        id="input-group-1"
        label="Nom du produit :"
        label-for="input-1"
      >
        <b-form-input
          id="input-1"
          v-model="form.name"
          type="text"
          placeholder="Nom du produit"
          required
        ></b-form-input>
      </b-form-group>
      <!-- DESCRIPTION -->

      <b-form-group
        id="input-group-1"
        label="Description :"
        label-for="input-1"
      >
        <b-form-input
          id="input-1"
          v-model="form.description"
          placeholder="Entrer la description"
          required
        ></b-form-input>
      </b-form-group>
      <!-- short DESCRIPTION -->

      <b-form-group
        id="input-group-1"
        label="Petit description :"
        label-for="input-1"
      >
        <b-form-input
          id="input-1"
          v-model="form.short_description"
          placeholder="Entrer une petite description"
          required
        ></b-form-input>
      </b-form-group>

      <!-- IMG / VALIDE -->
      <b-form-group
        id="input-group-1"
        label="Ajouter une image :"
        label-for="input-1"
      >
        <input type="file" @change="onFileSelected" />
        <b-button id="button" @click="onUpload" pill variant="outline-secondary"
          >Upload</b-button
        >

        <progress max="100" :value.prop="uploadPercentage"></progress>
        <p v-if="form.idIMG">OK</p>
        <p v-if="!form.idIMG && file">Uploading</p>
      </b-form-group>
    </section>

    <section class="general">
      <div>
        <b-card no-body class="overflow-hidden" style="max-width: 540px">
          <b-row no-gutters>
            <b-col md="3" class="btnGeneral">
              <b-button @click="generalBtn()" variant="dark">Général</b-button>
              <b-button @click="produitBtn()" variant="dark"
                >Produits liés</b-button
              >
              <b-button
                v-show="selectedProduct === 'Variable'"
                @click="attributBtn()"
                variant="dark"
                >Produit variable</b-button
              >
            </b-col>
            <b-col md="9">
              <b-card-body title="">
                <b-card-text class="test" v-show="geneBtn">
                  <!-- PRICE -->
                  <b-form-group
                    id="input-group-2"
                    label="Prix :"
                    label-for="input-2"
                  >
                    <b-form-input
                      id="input-2"
                      v-model="form.price"
                      type="number"
                      placeholder="€"
                      required
                    ></b-form-input>
                  </b-form-group>
                  <!-- SALE PRICE -->
                  <b-form-group
                    id="input-group-2"
                    label="Prix promo :"
                    label-for="input-2"
                  >
                    <b-form-input
                      id="input-2"
                      v-model="form.salePrice"
                      type="number"
                      placeholder="promo en €"
                    ></b-form-input>
                  </b-form-group>
                  <!-- QTE STOCK -->
                  <input type="checkbox" id="checkbox" v-model="form.manageStock" />
                  <label for="checkbox" v-if="!form.manageStock ">Gérer le stock</label>
                  <b-form-group
                  v-if="form.manageStock"
                    id="input-group-2"
                    label="Quantité de stock :"
                    label-for="input-2"
                  >
                    <b-form-input
                      id="input-2"
                      type="number"
                      v-model="form.stock_quantity"
                      required
                    ></b-form-input>
                  </b-form-group>

                  <!-- CATEGORIE -->
                  <b-form-group id="" label="catégorie " label-for="input-2">
                    <select
                      v-model="selectedCategorie"
                      @change="getCategoriesId(selectedCategorie)"
                    >
                      <option
                        v-for="(cat, key) in categories"
                        :value="key"
                        :key="key"
                      >
                        {{ cat.name }}
                      </option>
                    </select>

                    <div v-for="(item, key) in categoriesId" :key="key">
                      {{ item.name }}
                    </div>
                  </b-form-group>
                </b-card-text>
                <b-card-text v-show="proBtn">
                  <!-- CROSS SELL ID -->
                  <b-form-group id="" label="cross sell " label-for="input-2">
                    <select
                      v-model="selectedCross"
                      @change="getcrossSellId(selectedCross)"
                    >
                      <option
                        v-for="(product, key) in products"
                        :value="key"
                        :key="key"
                      >
                        {{ product.name }}
                      </option>
                    </select>

                    <div v-for="(item, key) in crossSellId" :key="key">
                      {{ item }}
                    </div>
                  </b-form-group>

                  <!-- UP SELL ID -->
                  <b-form-group id="" label="up sell " label-for="input-2">
                    <select
                      v-model="selectedUpSell"
                      @change="getUpSellId(selectedUpSell)"
                    >
                      <option
                        v-for="(product, key) in products"
                        :value="key"
                        :key="key"
                      >
                        {{ product.name }}
                      </option>
                    </select>

                    <div v-for="(item, key) in upSellId" :key="key">
                      {{ item }}
                    </div>
                  </b-form-group>
                </b-card-text>
                <b-card-text
                  v-show="attriBtn && selectedProduct === 'Variable'"
                >
                  <!-- ATTRIBUTES -->
                  <b-form-group
                    id=""
                    label="Choisir un attribut : "
                    label-for="input-2"
                  >
                    <select
                      v-model="selectedAttributes"
                      @change="
                        (attributeID = attributes[selectedAttributes]),
                          getTermes(attributes[selectedAttributes].id)
                      "
                    >
                      <option
                        v-for="(attribute, key) in attributes"
                        :value="key"
                        :key="key"
                      >
                        {{ attribute.name }}
                      </option>
                    </select>
                    {{ attributeID.id }}
                  </b-form-group>
                  <b-button
                    id="button"
                    @click="addProduct"
                    v-if="termes[0] && selectedAttributes"
                    pill
                    variant="outline-secondary"
                    >Choisir</b-button
                  >
                  <p v-if="selectedAttributes && !termes[0]">En chargement</p>

                  <p v-if="newProductId && !produitBool">OK</p>
                  <p v-if="!newProductId && produitBool">
                    Ajout du produit ...
                  </p>
                  <!-- PRODUIT VARIANT -->

                  <br />
                  <div v-show="newProductId">
                    Ajouter les variances :
                    <addproductvariant
                      :products="products"
                      :newProductId="newProductId"
                      :termes="termes"
                      :attributeID="attributeID"
                    />
                  </div>
                </b-card-text>
              </b-card-body>
            </b-col>
          </b-row>
        </b-card>
      </div>
    </section>
    <section class="variable"></section>
    <section v-show="selectedProduct === 'Simple'">
      <button  @click="addProduct">add</button>
    </section>
  </div>
</template>

<script>
import axios from "axios";
import addproductvariant from "@/components/products/addproductvariant";
export default {
  components: {
    addproductvariant,
  },
  data() {
    return {
      geneBtn: true,
      proBtn: false,
      attriBtn: false,
      selectedProduct: "Simple",
      options: [
        { value: "Simple", text: "Produit simple" },
        { value: "Variable", text: "Produit variable" },
      ],
      form: {
        name: "",
        description: "",
        short_description: "",
        price: null,
        salePrice: null,
        stock_quantity: null,
        idIMG: 0,
        type: "simple",
        manageStock: false,
      },
      file: false,
      produitBool: false,
      attributes: [],
      selectedAttributes: "",
      attributeID: "",
      termes: [],
      simple: false,
      newProductId: "",
      products: [],
      categories: [],
      selectedCross: "",
      selectedUpSell: "",
      selectedCategorie: "",
      crossSellId: [],
      upSellId: [],
      categoriesId: [],
      uploadPercentage: 0,
      selectedFile: null,
      token: localStorage.getItem("token"),
    };
  },
  mounted() {
    this.getProduct();
    this.getcategorie();
    this.getAttributes();
  },
  methods: {
    onFileSelected(event) {
      this.selectedFile = event.target.files[0];
    },
    onUpload() {
      this.file = true;
      const fd = new FormData();
      fd.append("file", this.selectedFile);
      fd.append("title", this.selectedFile.name);

      console.log(this.selectedFile);
      axios
        .post(window.addresse + "wp-json/wp/v2/media", fd, {
          onUploadProgress: (uploadEvent) => {
            this.uploadPercentage = parseInt(
              Math.round((uploadEvent.loaded / uploadEvent.total) * 100)
            );
          },
          headers: { Authorization: "Bearer " + this.token },
        })
        .then(
          (response) => (
            (this.form.idIMG = response.data.id), (this.file = false)
          )
        );
    },
    addProduct() {
      console.log(this.form.manageStock)
      this.produitBool = true;
      let termesName = [];
      for (let i = 0; i < this.termes.length; i++) {
        termesName.push(this.termes[i].name);
      }

      if (this.selectedProduct === "Simple") {
        this.form.type = "simple";
      } else {
        this.form.type = "variable";
      }

      if(this.form.idIMG){axios
        .post(
          window.addresse + "wp-json/wc/v3/products",
          {
            name: this.form.name,
            description: this.form.description,
            short_description: this.form.short_description,
            regular_price: this.form.price,
            manage_stock: this.form.manageStock,
            sale_price: this.form.salePrice,
            stock_quantity: this.form.stock_quantity,
            cross_sell_ids: this.crossSellId,
            upsell_ids: this.upSellId,
            categories: this.categoriesId,
            type: this.form.type,
            attributes: [
              {
                id: this.attributeID.id,
                name: this.attributeID.name,
                visible: true,
                variation: true,
                options: termesName,
              },
            ],
            images: [
              {
                id: this.form.idIMG,
              },
            ],
          },
          { headers: { Authorization: "Bearer " + this.token } }
        )
        .then(
          (response) => (
            (this.newProductId = response.data.id),
            console.log(response),
            (this.produitBool = false)
          )
        );}
        else{
          axios
        .post(
          window.addresse + "wp-json/wc/v3/products",
          {
            name: this.form.name,
            description: this.form.description,
            short_description: this.form.short_description,
            regular_price: this.form.price,
            manage_stock: this.form.manageStock,
            sale_price: this.form.salePrice,
            stock_quantity: this.form.stock_quantity,
            cross_sell_ids: this.crossSellId,
            upsell_ids: this.upSellId,
            categories: this.categoriesId,
            type: this.form.type,
            attributes: [
              {
                id: this.attributeID.id,
                name: this.attributeID.name,
                visible: true,
                variation: true,
                options: termesName,
              },
            ]
          },
          { headers: { Authorization: "Bearer " + this.token } }
        )
        .then(
          (response) => (
            (this.newProductId = response.data.id),
            console.log(response),
            (this.produitBool = false)
          )
        );
        }
      
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
    // AFFICHE LES CATEGORIES
    getcategorie() {
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
    getAttributes() {
      axios
        .get(window.addresse + "wp-json/wc/v3/products/attributes", {
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then(
          (response) => (
            (this.attributes = response.data), console.log(this.attributes)
          )
        )
        .catch((error) => console.log(error));
    },
    async getTermes(id) {
      await axios
        .get(
          window.addresse +
            "wp-json/wc/v3/products/attributes/" +
            id +
            "/terms",
          {
            headers: {
              Authorization: "Bearer " + this.token,
            },
          }
        )
        .then(
          (response) => (
            (this.termes = response.data), console.log(this.termes)
          )
        )
        .catch((error) => console.log(error));
    },
    getcrossSellId(item) {
      return this.crossSellId.push(this.products[item].id);
    },
    getUpSellId(item) {
      return this.upSellId.push(this.products[item].id);
    },
    getCategoriesId(item) {
      return this.categoriesId.push(this.categories[item]);
    },
    generalBtn() {
      (this.attriBtn = false), (this.geneBtn = true), (this.proBtn = false);
    },
    produitBtn() {
      (this.attriBtn = false), (this.geneBtn = false), (this.proBtn = true);
    },
    attributBtn() {
      (this.attriBtn = true), (this.geneBtn = false), (this.proBtn = false);
    },
  },
};
</script>


<style >
.typeProduct {
  margin-bottom: 5%;
}
.produit {
  border: 3px outset rgba(28, 110, 164, 0.2);
  border-radius: 13px;
  -webkit-box-shadow: 0px 2px 21px 1px rgba(51, 51, 51, 0.88);
  box-shadow: 0px 2px 21px 1px rgba(51, 51, 51, 0.88);
  padding: 2em;
}
.general {
  margin-top: 5%;
}
.overflow-hidden {
  min-width: 100%;
}
.btnGeneral {
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
}

.btnGeneral button {
  margin: 0.2em;
}

#input-group-2 {
  display: inline-block;
  width: 140px;
  text-align: left;
}
​ #input-2 {
  width: 20%;
}
#button {
  box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
}
</style>