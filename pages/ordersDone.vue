<template>
  <div>
      <div
        v-if="ready === 0"
        id="chargement"
        style="
          width: 150px;
          height: 50px;
          position: absolute;
          top: 45%;
          left: 45%;
          color: red;
          font-weight: bold;
          font-size: 14px;
        "
      >
        <img src="../static/loader.gif" /> Chargement ...
      </div>
    <div v-for="(commande, idCommande) in ordersDone" :key="idCommande">
      <table
        class="table table-bordered"
        v-show="commande.status === 'completed'"
      >
        <thead v-if="commande" @click="showAllOrder(idCommande)"> 

          <tr>
            <th class="table-dark titreCommande" scope="col">
              <h3>{{ commande.id }}</h3>
              <h5 >
                {{ commande.order_key }} | {{ commande.date_created}}
                
              </h5>
              <h5>{{ commande.total }} €</h5>
            </th>
          </tr>
        </thead> 
         
         <tbody
         v-show="showAll && idTab === idCommande" 
          class="tdBody"
          v-for="(item, id) in commande.line_items"
          :key="id"
        >
          <div v-if="item.variation_id != 0">
            <tr class="table-info" v-if="id === 0">
              <td>ID_variant</td>
              <td>Nom_variant</td>
              <td>Quantité</td>
              <td>Prix unitaire</td>
              <td>Prix total HT</td>
            </tr>
            <tr>
              <td>{{ item.variation_id }}</td>
              <td class="tdNameVar">{{ item.name }}</td>
              <td>{{ item.quantity }}</td>
              <td>{{ item.price }} €</td>
              <td>{{ item.subtotal }} €</td>
            </tr>
          </div>
          <div v-if="item.variation_id === 0">
            <tr class="table-info" v-if="id === 0">
              <td>ID_produit</td>
              <td>Nom</td>
              <td>Quantité</td>
              <td>Prix unitaire</td>
              <td>Prix total HT</td>
            </tr>
            <tr>
              <td>{{ item.product_id }}</td>
              <td>{{ item.name }}</td>
              <td>{{ item.quantity }}</td>
              <td>{{ item.price }} €</td>
              <td>{{ item.subtotal }} €</td>
            </tr>
          </div>
        </tbody>
        <tfoot 
         v-show="showAll && idTab === idCommande"
        >
          <tr v-if="idCommande === 0">
            <td class="tdFooter">
              <h4>
                {{ commande.billing.first_name }}
                {{ commande.billing.last_name }}
              </h4>

              <div>
                <h6>Info client :</h6>

                <p class="infoClient1">
                  {{ commande.billing.address_1 }}
                  {{ commande.billing.address_2 }}
                  {{ commande.billing.city }}
                  {{ commande.billing.postcode }}
                  {{ commande.billing.country }}
                </p>
                <p class="infoClient1">
                  {{ commande.billing.email }}
                </p>
                <p>
                  {{ commande.billing.phone }}
                </p>
              </div>
            </td>
          </tr>

        </tfoot>
      </table>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      ordersDone: [],
      token: localStorage.getItem("token"),
      dateG: "",
      date: "",
      ready: 0,
      showAll: false,


    };
  },
  mounted() {
    this.getOrdersDone();
  },

  methods: {
    // test(date) {
    //   date = date.replace("T", " ");
    //   this.dateG = date;
    // },
    async getOrdersDone() {
        this.ready = 0;

      await axios
        .get(window.addresse + "wp-json/wc/v3/orders", {
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then(
          (response) => (this.ordersDone = response.data),
          console.log(this.ordersDone)
        )
        .catch((error) => console.log(error));
      this.ready = 1;

        // this.test(date)

    },
    
    showAllOrder(id){
      this.showAll = !this.showAll
      this.idTab = id
    }
  },
};
</script>

<style>
</style>