<template>
  <div>
    <div id="app">
        <input type="file" @change="onFileSelected" />
        <div id="preview">
          <img v-if="url" :src="url" />
        </div>
      </div>
      <b-button id="button" @click="onUpload" pill variant="outline-secondary"
        >Upload</b-button>
    <input type="text" v-model="nameEdit" placeholder="nom" />
    <input type="number" v-model="priceEdit" placeholder="€" />
    <button @click="editProduct()">valider</button>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "editProduct",
  props: ["idEditProduct", "product"],
  data() {
    return {
      nameEdit: this.product.name,
      priceEdit: this.product.regular_price,
      token: localStorage.getItem("token"),
      form: {
        idIMG: 0,
      },
      selectedFile: null,
      url: null,
      file: true,
    };
  },
  methods: {
    onFileSelected(event) {
      this.selectedFile = event.target.files[0];
      this.url = URL.createObjectURL(this.selectedFile);
    },
    onUpload() {
      this.file = true;
      const fd = new FormData();
      fd.append("file", this.selectedFile);
      fd.append("title", this.selectedFile.name);
      const url =
        window.addresse +
        "wp-json/wp/v2/media/" +
        this.product.images[0].id;
        axios
          .delete(url, {
            data: {
              force: true,
            },
            headers: {
              Authorization: "Bearer " + this.token,
            },
          })
          .then((res) => console.log(res))
          .catch((error) => {
            console.log(error);
          });
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
            (this.form.idIMG = response.data.id),
            (this.file = false),
            this.newImg()
          )
        );
    },
    async newImg() {
      console.log(this.form.idIMG);
      console.log(this.product.id)
      const url =
        window.addresse +
        "/wp-json/wc/v3/products/" +
        this.product.id;
        
      await axios
        .put(
          url,
          {
           images: [
              {
                id: this.form.idIMG,
              },
            ],
          },
          {
            headers: {
              Authorization: "Bearer " + this.token,
            },
          }
        )
        .then((res) => console.log(res))
        .catch((error) => {
          console.log(error);
        });
    },
    editProduct() {
      alert(this.idEditProduct);
      const url =
        window.addresse + "wp-json/wc/v3/products/" + this.idEditProduct;
      axios
        .put(
          url,
          {
            name: this.nameEdit,
            regular_price: this.priceEdit,
          },
          {
            headers: {
              Authorization: "Bearer " + this.token,
            },
          }
        )
        .then((res) => console.log(res))
        .catch((error) => {
          console.log(error);
        });
    },
  },
};
</script>

<style>
</style>