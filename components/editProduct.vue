<template>
  <div class="editMain">
      <input type="file" @change="onFileSelected" class="inputFile"/>
      <div id="preview">
        <img class="img-fluid" v-if="url" :src="url" />
      </div>
    <b-button id="button" @click="onUpload" pill variant="outline-secondary"
      >Upload</b-button
    >
    <input type="text" v-model="form.nameEdit" placeholder="nom" />
    <input
      type="number"
      v-show="(product.type = 'simple')"
      v-model="form.priceEdit"
      placeholder="€"
    />
    <select v-model="form.statusEdit">
      <option disabled value="">Please select one</option>
      <option>publish</option>
      <option>private</option>
    </select> <br>
    <span>Selected: {{ form.statusEdit }}</span> <br>
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
      token: localStorage.getItem("token"),
      form: {
        idIMG: 0,
        statusEdit: this.product.status,
        nameEdit: this.product.name,
        priceEdit: this.product.regular_price,
      },
      selectedFile: null,
      url: null,
      file: true,
      checkImgEdit: false
    };
  },
  mounted() {},
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
      // SUPPRIME L IMG
      if(this.product.images[0]){
        const url =
        window.addresse + "wp-json/wp/v2/media/" + this.product.images[0].id;
      axios
        .delete(url, {
          data: {
            force: true,
          },
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then((res) => console.log(res),console.log("supp ok"))
        .catch((error) => {
          console.log(error);
        });
      }
      // AJOUTE NOUVELLE IMG AU MEDIA
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
            (this.form.idIMG = response.data.id), (this.file = false),this.checkImgEdit=true, console.log('ajout img ok'),console.log(this.form.idIMG)
          )
        );
    },
    editProduct() {
      // EDIT NEW PRODUIT
      console.log(this.checkImgEdit)
      const url =
        window.addresse + "wp-json/wc/v3/products/" + this.idEditProduct;
      if ( this.checkImgEdit) {
        axios
          .put(
            url,
            {
              name: this.form.nameEdit,
              regular_price: this.form.priceEdit,
              status: this.form.statusEdit,
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
          .then((res) => console.log(res), console.log('modif ok'), this.checkImgEdit=false)
          .catch((error) => {
            console.log(error);
          });
      } else {
        axios
          .put(
            url,
            {
              name: this.form.nameEdit,
              regular_price: this.form.priceEdit,
              status: this.form.statusEdit,
              
            },
            {
              headers: {
                Authorization: "Bearer " + this.token,
              },
            }
          )
          .then((res) => console.log(res), console.log('non'))
          .catch((error) => {
            console.log(error);
          });
      }
     },
  },
};
</script>

<style scoped>
.inputFile{
  width: 100%;
  padding: 1%
}
.editMain input{
  margin: 1%;

}
</style>