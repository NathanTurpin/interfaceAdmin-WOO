<template>
  <div>
    <!-- ATTRIBUTE -->
    <section class="attributes"></section>

    <!-- TERMES -->
    <section class="termes">
      <div v-for="(terme, idTerme) in termes" :key="idTerme">
        {{ terme.name }}

        <input
          type="number"
          placeholder="€"
          v-model="termes[idTerme].regularPrice"
        />
        <input type="file" @change="onFileSelected" />
        <b-button
          id="button"
          @click="onUpload(idTerme)"
          pill
          variant="outline-secondary"
          >Upload</b-button
        >
        <button
          v-if="form.idIMG && !file && idTab === idTerme"
          @click="addVariants(idTerme)"
        >
          add variant
        </button>
        <p v-if="file && idTab === idTerme">Uploading</p>
      </div>
    </section>

    <!-- PRODUITS VARIANTS -->
    <section class="variants"></section>
  </div>
</template>

<script>
import axios from "axios";
export default {
  props: ["products", "newProductId", "termes", "attributeID"],
  data() {
    return {
      token: localStorage.getItem("token"),
      form: {
        idIMG: 0,
      },
      selectedFile: null,
      file: false,
      idTab: null,
      status: "",
    };
  },
  mounted() {},
  methods: {
    onFileSelected(event) {
      this.selectedFile = event.target.files[0];
    },
    onUpload(idTerme) {
      this.file = true;
      this.idTab = idTerme;
      console.log(this.idTab);
      console.log(idTerme);

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
            (this.form.idIMG = response.data.id),
            (this.file = false),
            console.log(this.form.idIMG)
          )
        );
    },
    addVariants(idTerme) {
      this.file = false;
      console.log(this.termes[idTerme].name);
      console.log(this.attributeID.name, this.attributeID.id);
      axios
        .post(
          window.addresse +
            "wp-json/wc/v3/products/" +
            this.newProductId +
            "/variations",
          {
            regular_price: this.termes[idTerme].regularPrice,
            attributes: [
              {
                id: this.attributeID.id,
                name: this.attributeID.name,
                option: this.termes[idTerme].name,
              },
            ],
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
        .then(
          (response) => (
            (this.status = response.status),
            (this.form.idIMG = null),
            console.log(this.status),
            this.validationAddProduct()
          )
        )
        .catch((error) => console.log(error));
    },
    validationAddProduct() {
      if (this.status === 201) {
        alert("produit ajouté");
      } else {
        alert("erreur");
      }
    },
  },
};
</script>

<style>
</style>