<template>
  <div>

     <input type="checkbox" name="" id="" v-model="simple">

    
   
    <section v-if="!simple"> Produit simple :
    <!-- NAME -->
    <b-form-input
      id="input-1"
      v-model="form.name"
      type="text"
      placeholder="Nom du produit"
      required
    ></b-form-input>

    <!-- DESCRIPTION -->

    <b-form-group id="input-group-2" label="description" label-for="input-2">
      <b-form-input
        id="input-2"
        v-model="form.description"
        placeholder="Entrer la description"
        required
      ></b-form-input>
    </b-form-group>
    <!-- short DESCRIPTION -->

    <b-form-group
      id="input-group-3"
      label="Petit description :"
      label-for="input-3"
    >
      <b-form-input
        id="input-3"
        v-model="form.short_description"
        required
      ></b-form-input>
    </b-form-group>
    <!-- PRICE -->

    <b-form-input
      id="input-1"
      v-model="form.price"
      type="number"
      placeholder="€"
      required
    ></b-form-input>
    <!-- SALE PRICE -->

    <b-form-input
      id="input-1"
      v-model="form.salePrice"
      type="number"
      placeholder="promo en €"
    ></b-form-input>
    <!-- QTE STOCK -->

    <b-form-group
      id="input-group-4"
      label="Quantité de stock :"
      label-for="input-4"
    >
      <b-form-input
        id="input-4"
        type="number"
        v-model="form.stock_quantity"
        required
      ></b-form-input>
    </b-form-group>

    <!-- CROSS SELL ID -->
    <b-form-group id="" label="cross sell " label-for="input-2">
      <select v-model="selectedCross" @change="getcrossSellId(selectedCross)">
        <option v-for="(product, key) in products" :value="key" :key="key">
          {{ product.name }}
        </option>
      </select>

      <div v-for="(item,key) in crossSellId" :key="key">
        {{ item }}
      </div>
    </b-form-group>

    <!-- UP SELL ID -->
    <b-form-group id="" label="up sell " label-for="input-2">
      <select v-model="selectedUpSell" @change="getUpSellId(selectedUpSell)">
        <option v-for="(product, key) in products" :value="key" :key="key">
          {{ product.name }}
        </option>
      </select>

      <div v-for="(item,key) in upSellId"  :key="key">
        {{ item }}
      </div>
    </b-form-group>
    <!-- CATEGORIE -->
     <b-form-group id="" label="catégorie " label-for="input-2">
      <select v-model="selectedCategorie" @change="getCategoriesId(selectedCategorie)">
        <option v-for="(cat, key) in categories" :value="key" :key="key">
          {{ cat.name }}
        </option>
      </select>

      <div v-for="(item,key) in categoriesId" :key="key">
        {{ item.name }}
      </div>
    </b-form-group>
    <!-- PRODUIT VARIANT -->
    <b-form-group>
      
    </b-form-group>
    <!-- IMG / VALIDE -->

    <input type="file" @change="onFileSelected" />
    <button @click="onUpload">Upload</button>
    <progress max="100" :value.prop="uploadPercentage"></progress>
    <button v-show="form.idIMG" @click="addProduct">add</button>
    {{ form.idIMG }}
    </section>
    <section v-else>
      Produit avec des variances :
      <addproductvariant />
    </section>
  </div>
</template>

<script>
import axios from "axios";
import addproductvariant from "@/components/products/addproductvariant"
export default {
 components:{
   addproductvariant
 },
  data() {
   
     return {
      form: {
        name: "",
        description: "",
        short_description: "",
        price: null,
        salePrice: null,
        stock_quantity: null,
        idIMG: 0,
      },
      simple:false,
      products: [],
      categories: [],
      selectedCross: "",
      selectedUpSell:"",
      selectedCategorie: "",
      crossSellId: [],
      upSellId : [],
      categoriesId : [],
      uploadPercentage: 0,
      selectedFile: null,
      token: localStorage.getItem("token"),
    };
  },
  mounted() {
    this.getProduct();
    this.getcategorie()
  },
  methods: {
    onFileSelected(event) {
      this.selectedFile = event.target.files[0];
    },
    onUpload() {
      const fd = new FormData();
      fd.append("file", this.selectedFile);
      fd.append("title", this.selectedFile.name);

      console.log(this.selectedFile);
      axios
        .post("http://applicommande.local/wp-json/wp/v2/media", fd, {
          onUploadProgress: (uploadEvent) => {
            this.uploadPercentage = parseInt(
              Math.round((uploadEvent.loaded / uploadEvent.total) * 100)
            );
          },
          headers: { Authorization: "Bearer " + this.token },
        })
        .then((response) => (this.form.idIMG = response.data.id));
    },
    addProduct() {
      axios
        .post(
          "http://applicommande.local/wp-json/wc/v3/products",
          {
            name: this.form.name,
            description: this.form.description,
            short_description: this.form.short_description,
            regular_price: this.form.price,
            manage_stock: true,
            sale_price: this.form.salePrice,
            stock_quantity: this.form.stock_quantity,
            cross_sell_ids: this.crossSellId,
            upsell_ids: this.upSellId,
            categories: this.categoriesId,
            images: [
              {
                id: this.form.idIMG,
              },
            ],
          },
          { headers: { Authorization: "Bearer " + this.token } }
        )
        .then((response) => console.log(response));
    },
    getProduct() {
      axios
        .get("http://applicommande.local/wp-json/wc/v3/products", {
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
        .get("http://applicommande.local/wp-json/wc/v3/products/categories",{
          headers: {
            Authorization: "Bearer " + this.token
          }})
        .then((response) => (this.categories = response.data,console.log( this.categories)))
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
  },
};
</script>


<style>
</style>