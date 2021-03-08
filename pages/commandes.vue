<template>
  <div class="main">
    <!-- <div>
      <div v-for="(commande) in commandes" :key="commande.id">
        <p>{{ commande.id }} {{ commande.status }} {{ commande.total }}</p>
       
        <div v-for="commandeItem in commande.line_items" :key="commandeItem.id">
          {{commandeItem.id}}

          <p>{{ commandeItem.product_id}} {{ commandeItem.name}}  {{commandeItem.quantity}}  {{commandeItem.total}}</p>
        </div>
         <li><commandeIDAdmin  /></li> 
     </div>
    </div> -->
    <button @click="getCommandes">ee</button>
    <p v-show="this.notif>=0"> {{ notif }}</p>
    <div v-for="commande in commandes" :key="commande.id">
      {{ commande }}
    </div>
  </div>
</template>

<script>
import axios from "axios";
import commandeIDAdmin from "@/components/commandeIDAdmin";

export default {
  components: {
    commandeIDAdmin,
  },
  data() {
    return {
      notif: -1,
      commandes: [],
      lastCommandes: [],
      token: localStorage.getItem("token"),
    };
  },
  mounted() {
    // AFFICHE LES CATEGORIES
    // setInterval(function () {
    //   self.notification();
    // }, 8000);
    var self = this;
    setInterval(function () {
      self.getCommandes();
    }, 10000);
  },
  methods: {
     async getCommandes() {
        
      await axios
        .get(window.addresse + "wp-json/wc/v3/orders", {
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then(
          (response) => (
            (this.commandes = response.data), console.log("actuel" ), console.log(this.commandes.length)
          )
        )
        .catch((error) => console.log(error));
        
      
     if(this.commandes.length>this.lastCommandes.length){
       this.notif+=1
     }
      this.lastCommandes = this.commandes;

      console.log("last:")
      console.log( this.lastCommandes.length)
    }
  },
};
</script>

<style>
</style>