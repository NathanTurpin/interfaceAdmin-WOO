<template>
  <div class="containerArticle">
    <p>
      titre: <input type="text" v-model="actu.title" /> contenu :
      <input type="text" v-model="actu.content" />
      <input type="text" v-model="actu.status" />
      <a href="#" @click="envoyer()" class="btn btn-success">Envoyer</a>
    </p>

    <div v-for="(product, index) in products" :key="index">
      <div class="card">
        <img
          v-if="product.images[0] !== undefined"
          :src="product.images[0].thumbnail"
          alt=""
          class="card-img-top"
        />
        <div class="card-body">
          <h5 class="card-title">{{ product.name }}</h5>
          <p class="card-text" v-html="product.short_description"></p>

          <p v-html="product.price_html"></p>
          <small class="text-muted" v-if="product.is_in_stock">En stock</small>
          <div class="card-footer">
            <a :href="product.add_to_cart.url">{{
              product.add_to_cart.text
            }}</a>
          </div>
        </div>
      </div>
    </div>
    <input type="file" @change="onFileSelected" />
    <button @click="onUpload">Upload</button>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      token: "",
      products: [],
      actu: {
        title: "",
        content: "",
        status: "",
      },
      selectedFile: null,
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
        .then((response) => console.log(response.data));
    },
    getProduct() {
      axios
        .get("http://applicommande.local/wp-json/wc/store/products")
        .then((response) => (this.products = response.data))
        .catch((error) => console.log(error));
    },
    envoyer() {
      axios
        .post(
          "http://applicommande.local/wp-json/wp/v2/posts",
          {
            title: this.actu.title,
            content: this.actu.content,
            status: this.actu.status,
          },
          { headers: { Authorization: "Bearer" + this.token } }
        )
        .then((response) => console.log(response.data))
        .catch((error) => console.log(error.response));
    },
  },
  mounted() {
    this.getProduct(),
      //RECUPERE LE TOKEN
      axios
        .post("http://applicommande.local/wp-json/jwt-auth/v1/token", {
          username: "admin",
          password: "admin",
        })
        .then((response) => (this.token = response.data.token))
        .catch((error) => console.log(error.response));
  },
};
</script>

<style scoped>
.containerArticle {
  display: flex;
  flex-wrap: wrap;
  width: 80%;
  margin: auto;
}
.card {
  margin: 1em 2em;
  width: 80%;
}
</style>
