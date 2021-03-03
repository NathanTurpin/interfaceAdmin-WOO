<template>
  <div>
    <b-modal id="modal-1" :title="product.name">
      <h5>
        {{ product.id }}
      </h5>
      <img
        class="card-img-top img-fluid"
        v-if="produit_variant.image !== undefined"
        :src="produit_variant.image.src"
        alt="Card image cap"
      />
      <div id="app">
        <input type="file" @change="onFileSelected" />

        <div id="preview">
          <img v-if="url" :src="url" />
        </div>
      </div>
      <b-button id="button" @click="onUpload" pill variant="outline-secondary"
        >Upload</b-button
      >
      <input type="number" v-model="priceEdit" />
      <button @click="editProVariant()">edit</button>
      <p class="my-4">{{ produit_variant }}</p>
    </b-modal>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: "seeProductVariant",
  props: ["produit_variant", "product"],
  data() {
    return {
      token: localStorage.getItem("token"),
      priceEdit: this.produit_variant.price,
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
     this.url = URL.createObjectURL( this.selectedFile );

    },
    onUpload() {
      this.file = true;
      const fd = new FormData();
      fd.append("file", this.selectedFile);
      fd.append("title", this.selectedFile.name);
      const url = window.addresse + "wp-json/wp/v2/media/"+ this.produit_variant.image.id;
      if(this.product.images[0].id !== this.produit_variant.image.id){
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
      }
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

      const url =
        window.addresse + "/wp-json/wc/v3/products/"+this.product.id +"/variations/"+ this.produit_variant.id;
      await axios
        .put(
          url,
          {
            image: {
              id: this.form.idIMG,
            },
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
    editProVariant() {
      alert(this.product.id);
      alert(this.produit_variant.id);
      const url =
        window.addresse +
        "/wp-json/wc/v3/products/" +
        this.product.id +
        "/variations/" +
        this.produit_variant.id;
      axios
        .put(
          url,
          {
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
  
};
</script>

<style>
</style>