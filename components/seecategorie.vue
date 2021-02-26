<template>
  <div class="container">
    <div class="search-wrapper">
    <input type="text" v-model="search" placeholder="Search title.."/>
        <label>Search title:</label>
        
  </div>
    <section id="app" class="content">
      <div v-for="(categorie, idcategorie) in filteredList" :key="idcategorie">
        <b-card
          header-tag="header"
          class="card-img mb-3"
          footer-tag="footer"
          
        >
          <template #header>
            <p v-if="categorie.salePrice" >{{categorie.salePrice}}</p>
            <p v-else v-html="categorie.price_html"></p>
            {{categorie.stock_quantity}}
          </template>
          <h5>{{ categorie.name }}</h5>
          <b-card-text> <p v-html="categorie.description"></p> </b-card-text>
          <template #footer>
            <button @click="deletecategorie(categorie.id)">supp</button>
            <button @click="showEditComponent(categorie.id)">edit</button>
            
          </template>
         </b-card><!--<div v-show="showEdit">
              <editcategorie :idEditcategorie="idEditcategorie" :categorie="categorie"/>
            </div> -->
      </div>
    </section>
  </div>
</template>

<script>
import axios from "axios";
// import editcategorie from "@/components/editcategorie";
export default {
//   components: {
//     editcategorie,
//   },
  data() {
    return {
      search: '',
      categories: [],
      token: localStorage.getItem("token"),
      showEdit: false,
      idEditcategorie: "",
    };
  },
  mounted() {
    this.getcategorie();
  },
  computed: {
    filteredList() {
      return this.categories.filter(categorie => {
        return categorie.name.toLowerCase().includes(this.search.toLowerCase())
      })
    }
  },
  methods: {
    // AFFICHE LES CATEGORIES
    getcategorie() {
      axios
        .get(window.addresse + "wp-json/wc/v3/products/categories",{
          headers: {
            Authorization: "Bearer " + this.token
          }})
        .then((response) => (this.categories = response.data,console.log( this.categories)))
        .catch((error) => console.log(error));
    },
    // DELETE categorie
    deletecategorie(idP) {
      alert(idP);
      const url = window.addresse + "wp-json/wc/v3/products/categories/" + idP;
      axios
        .delete(url, {
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then((res) => console.log(res))
        .catch((error) => {
          console.log(error);
        });
    },
    // EDIT categorie
    showEditComponent(id) {
      this.showEdit = !this.showEdit;
      this.idEditcategorie = id;
    },
  },
};
</script>

<style scoped>

.card-img img{
  max-width: 300px;
  max-height: 300px;
}
</style>
