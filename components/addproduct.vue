<template>
  <div>
      <b-form-input
        id="input-1"
        v-model="form.name"
        type="text"
        placeholder="Nom du produit"
        required
      ></b-form-input>

      <b-form-group id="input-group-2" label="description" label-for="input-2">
        <b-form-input
          id="input-2"
          v-model="form.description"
          placeholder="Entrer la description"
          required
        ></b-form-input>
      </b-form-group>

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
      <b-form-input
        id="input-1"
        v-model="form.price"
        type="number"
        placeholder="€"
        required
      ></b-form-input>
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

      <input type="file" @change="onFileSelected" />
      <button @click="onUpload">Upload</button>
      <button @click="addProduct">add</button>
  {{form.idIMG}}
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
        short_description : "",
        price: null,
        stock_quantity: null,
        idIMG: ""
      },
      selectedFile: null,
      token: localStorage.getItem("token"),
    };
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
        .post(
          "http://applicommande.local/wp-json/wp/v2/media",
          fd,
          { onUploadProgress: (uploadEvent) => {
              console.log(
                "Upload Progress :" +
                  Math.round((uploadEvent.loaded / uploadEvent.total) * 100) +
                  "%"
              );
            },
            headers: { Authorization: "Bearer " + this.token },
          }
        )
        .then((response) => this.form.idIMG = response.data.id);
    },
    addProduct() {
      axios.post(
        "http://applicommande.local/wp-json/wc/v3/products",
        {
          name: this.form.name,
          description: this.form.description,
          short_description: this.form.short_description,
           regular_price: this.form.price ,
          stock_quantity: this.form.stock_quantity,
          images : [{ 
            id :this.form.idIMG
          }]
        },
        { headers: { Authorization: "Bearer " + this.token } })
        .then((response) => console.log(response))
    },
  },
};
</script>


<style>
</style>