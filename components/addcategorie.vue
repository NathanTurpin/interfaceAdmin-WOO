<template>
  <div>
    <!-- NAME -->
    <b-form-input
      id="input-1"
      v-model="form.name"
      type="text"
      placeholder="Nom de la catégorie"
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

    <button @click="addCategorie">add</button>

  </div>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      token: localStorage.getItem("token"),
      form: {
        name: "",
        description: "",
      },
    };
  },
  methods: {
    addCategorie() {
      axios
        .post(
          "http://applicommande.local/wp-json/wc/v3/products/categories",
          {
            name: this.form.name,
            description: this.form.description,
          },
          { headers: { Authorization: "Bearer " + this.token } }
        )
        .then((response) => console.log(response));
    },
  },
};
</script>

<style>
</style>