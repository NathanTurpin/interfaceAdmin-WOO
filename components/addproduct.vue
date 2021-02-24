<template>
  <div>
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
      <!-- RELETED ID -->

      <select v-model="selected" @change="getReletedID(selected)">
        <option v-for="(product, key) in products" :value="key" :key="key">
          {{ product.name }}
        </option>
      </select>

      <div v-for="item in reletedId">
        {{ item }}
      </div>
      <button @click="test">c</button>
    </b-form-group>
    <!-- IMG / VALIDE -->

    <input type="file" @change="onFileSelected" />
    <button @click="onUpload">Upload</button>
    <progress max="100" :value.prop="uploadPercentage"></progress>
    <button v-show="form.idIMG" @click="addProduct">add</button>
    {{ form.idIMG }}
  </div>
</template>

<script>
import axios from "axios";

export default {
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
      products: [],
      selected: "",
      reletedId: [],
      uploadPercentage: 0,
      selectedFile: null,
      token: localStorage.getItem("token"),
    };
  },
  mounted() {
    this.getProduct();
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
    test(){
      
      let crossId=this.reletedId
      console.log(crossId)
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
            cross_sell_ids: this.reletedId,
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
    getReletedID(item) {
      return this.reletedId.push(this.products[item].id);
    },
  },
};
</script>


<style>
</style>