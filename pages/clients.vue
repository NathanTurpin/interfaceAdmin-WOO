<template>
  <div>
    <div class="search-wrapper">
      <input type="text" v-model="search" placeholder="Search title.." />
      <label>Search title:</label>
    </div>
    <section id="app" class="content">
      <div v-for="client in filteredList">
      {{ client.username }} <br>
      {{client.email}}

      </div>
    </section>
  </div>
</template>

<script>
import axios from "axios";
import editProduct from "@/components/editProduct";
export default {
  components: {
    editProduct,
  },
  data() {
    return {
      search: "",
      clients: [],
      token: localStorage.getItem("token"),
    };
  },
  mounted() {
    this.getClients();
  },
  computed: {
    filteredList() {
      return this.clients.filter((client) => {
        return client.username.toLowerCase().includes(this.search.toLowerCase());
      });
    },
  },
  methods: {
    // AFFICHE LES CLIENTS
    getClients() {
      axios
        .get("http://applicommande.local/wp-json/wc/v3/customers",{
          headers: {
            Authorization: "Bearer " + this.token,
          }})
        .then((response) => (this.clients = response.data))
        .catch((error) => console.log(error));
    },
  },
};
</script>

<style>
.content {
  display: flex;
  flex-wrap: wrap;
}
.card {
  width: 60%;
  min-height: 50vh;
}
</style>
