<template>
  <div class="main">
    <div class="table-responsive">
<div v-if="ready===0" id="chargement" style="width:150px;height:50px;position:absolute;top:45%;left:45%;color:red;font-weight:bold;font-size:14px;">
   <img  src="../static/loader.gif" /> Chargement ...
</div>
      <table class="table table-bordered" v-for="(commande,idCommande) in commandes" :key="idCommande">
        <thead v-if="commande">
           
            <tr class="titreCommande" >
              <th class="table-dark" scope="col"> <h3> {{ commande.id }} </h3>| <h5> {{ commande.order_key }} | {{ commande.date_created }} | {{ commande.total}}</h5></th>
              
            </tr>
        </thead>
       <tbody v-for="(item,id) in commande.line_items" :key="id">
            <div v-if="item.variation_id!=0">
              <tr v-if="id===0">
                <td>ID_variant</td>
                <td>Nom_variant</td>
                <td>Variant</td>  
                <td>Quantité</td>
                <td>Prix unitaire</td>
                <td>Prix total HT</td>
                <td>Image produit</td>
              </tr> 
              <tr>
                <td>{{ item.variation_id }}</td>
                <td>{{ item.name }}</td>
                 <td v-if="listeCommande[idCommande][id]!==undefined ">{{ listeCommande[idCommande][id].attributes[0].option }}</td>
                <td>{{ item.quantity }}</td>
                <td>{{ item.price}} €</td>
                <td>{{ item.subtotal}} €</td>
                 <td v-if="listeCommande[idCommande][id]!==undefined"> <img class="tdImg" v-if="listeCommande[idCommande][id].image !== undefined" :src="listeCommande[idCommande][id].image.src" alt="Card image cap"/> </td>
           </tr> 
             </div> 
            <div v-if="item.variation_id===0 ">
              <tr v-if="id===0">
                <td>ID_produit</td>
                <td>Nom</td>
                <td></td>
                <td>Quantité</td>
                <td>Prix unitaire</td>
                <td>Prix total HT</td>
                <td>Image produit</td>

              </tr>
              <tr> 
                 <td>{{ item.product_id }}</td>
                <td>{{ item.name }}</td>
                <td></td>
                <td>{{ item.quantity }}</td>
                <td>{{ item.price}} €</td>
                <td>{{ item.subtotal}} €</td>
                <td v-if="listeCommande[idCommande][id]!==undefined"> <img  class="tdImg" v-if="listeCommande[idCommande][id].images[0] !== undefined" :src="listeCommande[idCommande][id].images[0].src" alt="Card image cap" /> </td> 
           </tr>
            </div> 
        </tbody> 
        <tfoot>
          <tr>
            <td>
              {{ commande.billing.first_name }}
              {{ commande.billing.last_name }}
            </td>
            <td>
              {{ commande.billing.address_1 }}
              {{ commande.billing.address_2 }}
              {{ commande.billing.city }}
              {{ commande.billing.postcode }}
              {{ commande.billing.country }}
            </td>
            <td>
              {{ commande.billing.email }}
              {{ commande.billing.phone }}
            </td>
          </tr>
          <tr>
            <select v-model="form.statusEdit">
              <option disabled value="">{{ commande.status }}</option>
              <option>completed</option>
            </select>
            <br />
            <span>Selected: {{ form.statusEdit }}</span>
            <br />
            {{
              commande.total
            }}
            <button @click="editOrders(commande.id)">valider</button>
          </tr>
        </tfoot>
    </table>
     
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
    return {form: {
        statusEdit: "",
      },
      ready:0,
      commandes: [],
      commandesLire:[],
      lastCommandes: [],
      token: localStorage.getItem("token"),
      listeCommande:[],
      listeCommandeLire:[],
      adresse:window.addresse+"/assets/loader.gif"
    };
  },
  mounted() {
    var self = this;
    self.getCommandes();
    setInterval(function () {
      self.getCommandes();
    }, 40000);
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
          (response) => (this.commandesLire = response.data),
        )
        .catch((error) => console.log(error));   
        if (this.commandesLire.length > this.lastCommandes.length) {
          this.ready=0
          for (let i = 0; i < this.commandesLire.length; i++) {        // pour chaque commande on crée un tableau des produits commandés
          var produit=[]
              for (let j = 0; j < this.commandesLire[i].line_items.length; j++) { // pour chaque ligne dans une commande on recupère les infos du produits commandé
              if(this.commandesLire[i].line_items[j].product_id!=='undefined'){
                await axios
                .get(window.addresse + "/wp-json/wc/v3/products/" + this.commandesLire[i].line_items[j].product_id, {
                  headers: {
                    Authorization: "Bearer " + this.token,
                  },
                })
                .then((response) => produit[j]=(response.data)) // on met le produit trouvé dans le tableau
                .catch((error) => console.log('toto')) 
              
                if(this.commandesLire[i].line_items[j].variation_id){ // si le produit est un variant on va chercher la variation du produit
                  await axios                 
                    .get(
                      window.addresse +
                        "/wp-json/wc/v3/products/" + produit[j].id+"/variations/"+this.commandesLire[i].line_items[j].variation_id,
                      {
                        headers: {
                          Authorization: "Bearer " + this.token,
                        },
                      }
                    )
                    .then((response) => produit[j]=(response.data)) // on met le produit variant trouvé dans le tableau
                    .catch((error) => console.log(error));
                }
              }
              }
            
          this.listeCommandeLire[i]=produit   // le tableau des produits de la commande est placé dans le tableau des commandes
          }
        this.lastCommandes = this.commandesLire;
        }
        this.commandes=this.commandesLire;
        this.listeCommande=this.listeCommandeLire;
        this.ready=1
    },
    editOrders(idCommande) {
      const url = window.addresse + "/wp-json/wc/v3/orders/" + idCommande;
      axios
        .put(
          url,
          {
            status: this.form.statusEdit,
          },
          {
            headers: {
              Authorization: "Bearer " + this.token,
            },
          }
        )
        .then((res) => console.log(res), console.log("modif ok"))
        .catch((error) => console.log(error));
    },
  }

};
</script>

<style scoped>
.titreCommande {
  display: flex;
  flex: 1 1 0;
}
.tdImg {
  max-width: 20%;
}
</style>