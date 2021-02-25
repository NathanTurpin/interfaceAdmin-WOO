<template>
  <div>
    <!-- ATTRIBUTE -->
    <section class="attributes">
      <h1>Créer vos attributs</h1>
      <!-- NAME -->
      <b-form-input
        id="input-1"
        v-model="nameAtttribute"
        type="text"
        placeholder="Nom de l'attribut"
        required
      ></b-form-input>
      <button @click="addAttributes()">add</button>
    </section>

    <!-- TERMES -->
    <section class="termes">
      <b-form-group id="" label="attribut " label-for="input-2">
        <select
          v-model="selectedAttribut"
          @change="attributId = attributs[selectedAttribut]"
        >
          <option v-for="(attribut, key) in attributs" :value="key" :key="key">
            {{ attribut.name }}
          </option>
        </select>

        {{ attributId.id }}
      </b-form-group>
      <b-form-input
        id="input-1"
        v-model="nameTerme"
        type="text"
        placeholder="Nom de du terme"
        required
      ></b-form-input>
      <button @click="addTerme()">add</button>
    </section>
  </div>
</template>

<script>
import axios from "axios";
export default {
  props: ["products"],
  data() {
    return {
      nameAtttribute: "",
      nameTerme: "",
      token: localStorage.getItem("token"),
      selectedAttribut: "",

      attributs: [],
      attributId: "",
    };
  },
  mounted() {
    this.getAttribut();
  },
  methods: {
    addAttributes() {
      axios
        .post(
          "http://applicommande.local/wp-json/wc/v3/products/attributes",
          {
            name: this.nameAtttribute,
          },
          { headers: { Authorization: "Bearer " + this.token } }
        )
        .then((response) => console.log(response));
    },
    getAttribut() {
      axios
        .get("http://applicommande.local/wp-json/wc/v3/products/attributes", {
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then(
          (response) => (
            (this.attributs = response.data), console.log(this.attributs)
          )
        )
        .catch((error) => console.log(error));
    },
    getAttributId(item) {
      return this.attributId.push(this.attributs[item]);
    },
    addTerme() {
      axios
        .post(
          "http://applicommande.local/wp-json/wc/v3/products/attributes/" +
            this.attributId.id +
            "/terms",
          {
            name: this.nameTerme,
          },
          {
            headers: {
              Authorization: "Bearer " + this.token,
            },
          }
        )
        .then((response) => console.log(response))
        .catch((error) => console.log(error));
    },
  },
};
</script>

<style>
</style>