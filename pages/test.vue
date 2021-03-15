<template>
  <div class="main">
    <!-- <p v-show="this.notif >= 0">{{ notif }}</p> -->
    <button @click="getProductImg">cc</button>
    {{ idProdImg }} <br />
    <br />
    <div class="table-responsive">
      <table
        class="table"
        v-for="(commande, idCommande) in commandes"
        :key="idCommande"
      >
        <thead>
          <tr>
            <th>
              {{ commande.id }} 
            </th>
            <th>
                {{ commande.order_key }}
            </th>
            <th>
              {{ commande.date_created }}

            </th>
          </tr>
        </thead>
        <tbody v-for="(item, id) in commande.line_items" :key="id">
          <tr>
            <td></td>
            <td>
              <p v-if="item.variation_id">ID variable</p>
              <p v-else>ID produit</p>
            </td>
            <td>Nom</td>
            <td>Quantité</td>
          </tr>
          <tr>
            <td>produits</td>
            <td>
              <p v-if="item.variation_id">{{ item.variation_id }}</p>
              <p v-else>{{ item.product_id }}</p>
            </td>

            <td>{{ item.name }}</td>
            <td>{{ item.quantity }}</td>

            <div class="tdImg" v-if="item.variation_id">
              <!-- <div> -->
              <!-- <td v-if="ready2 === 1"> -->
              <!-- {{ item.variation_id }}  -->
              <!-- {{ listeCommandeVar[idCommande][id].variations[id] }} -->
              <!-- {{ listeCommandeVar[idCommande][id].variations }} -->
              <!-- <div
                      v-for="(item,idee) in listeCommandeVar[idCommande][id]
                        .variations" :key="idee"
                    >
                     {{idee}} {{ item[1] }}

                    </div> -->

              <!-- <img
                    class="img-fluid"
                    v-if="listeCommande[idCommande][id].variations[id].image !== undefined"
                    :src="listeCommande[idCommande][id].variations[id].image.src"
                    alt="Card image cap"
                  /> -->
              <!-- </td>
              </div> -->
              <!-- <div v-for="variant in productsVariants">
                <div v-for="vari in variant">
                  <div v-if="item.variation_id === vari.id">
                    <td>
                      <img
                        class="img-fluid"
                        v-if="vari.image !== undefined"
                        :src="vari.image.src"
                        alt="Card image cap"
                      />
                    </td>
                  </div>
                </div>
              </div> -->
            </div>
            <div class="tdImg" v-else>
              <!-- <td>{{idCommande}}{{listeCommande[idCommande][id].images[0].src}}</td> -->
              <td v-if="ready === 1">
                <img
                  class="img-fluid"
                  v-if="listeCommande[idCommande][id].images[0] !== undefined"
                  :src="listeCommande[idCommande][id].images[0].src"
                  alt="Card image cap"
                />
              </td>

              <!-- <div v-for="(product, id) in products" :key="id"> -->
              <!-- <div v-if="item.product_id === product.id"> -->
              <!-- <td>
                    <img
                      class="img-fluid"
                      v-if="listeCommande[idCommande][id].images[0] !== undefined"
                      :src="listeCommande[idCommande][id].images[0].src"
                      alt="Card image cap"
                    />
                    </td> -->
              <!-- </div> -->
              <!-- </div> -->
            </div>
          </tr>
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
    <!--  -->
    <!-- 
      fin -->
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
      // notif: -1,
      ready: 0,
      ready2: 0,
      form: {
        statusEdit: "",
      },
      commandes: [],
      idCommande: 0,
      lastCommandes: [],
      listeCommandeVar: [],
      idProdImg: [],
      idProdVarImg: [],
      products: [],
      productsVariants: [],
      token: localStorage.getItem("token"),
      listeCommande: [],
    };
  },
  mounted() {
    var self = this;
    // this.getProductImg();

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
          (response) => (this.commandes = response.data)
          // console.log(this.commandes),
          // console.log(this.commandes.length)
        )
        .catch((error) => console.log(error));

      if (this.commandes.length > this.lastCommandes.length) {
        this.getProductImg();
      }
      this.lastCommandes = this.commandes;
    },

    async getProductImg() {
      this.listeCommande = [];
      this.listeCommandeVar = [];
      console.log(this.commandes);

      for (let i = 0; i < this.commandes.length; i++) {
        var prod = [];
        for (let j = 0; j < this.commandes[i].line_items.length; j++) {
          prod[j] = { produit: this.commandes[i].line_items[j].product_id }; // renvoie toutes les produits de la commande
        }
        this.listeCommande[i] = prod;
        this.listeCommandeVar[i] = prod;
      }
      console.log(this.listeCommande);
      for (let k = 0; k < this.listeCommande.length; k++) {
        for (let l = 0; l < this.listeCommande[k].length; l++) {
          await axios
            .get(
              window.addresse +
                "/wp-json/wc/v3/products/" +
                this.listeCommande[k][l].produit,
              {
                headers: {
                  Authorization: "Bearer " + this.token,
                },
              }
            )
            .then((response) => (this.listeCommande[k][l] = response.data))
            .catch((error) => console.log(error));
        }
      }
      this.ready = 1;
      console.log(this.ready2);
      //   for (let k = 0; k < this.listeCommandeVar.length; k++) {
      //     for (let l = 0; l < this.listeCommandeVar[k].length; l++) {
      //       for (
      //         let b = 0;
      //         b < this.listeCommandeVar[k][l].variations.length;
      //         b++
      //       ) {
      //         await axios
      //           .get(
      //             window.addresse +
      //               "/wp-json/wc/v3/products/" +
      //               this.listeCommandeVar[k][l].id +
      //               "/variations/" +
      //               this.listeCommandeVar[k][l].variations[b],
      //             {
      //               headers: {
      //                 Authorization: "Bearer " + this.token,
      //               },
      //             }
      //           )
      //           .then(
      //             (response) =>
      //               (this.listeCommandeVar[k][l].variations[b] = response.data),
      //             console.log(this.listeCommandeVar[k][l].variations[b])
      //           )
      //           .catch((error) => console.log(error));
      //       }
      //     }
      //   }
      //   this.ready2 = 1;
      //   console.log(this.ready2);
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
  },
};
</script>

<style scoped>
.table {
  margin-bottom: 2%;
}
.tdImg {
  max-width: 20%;
}
tfoot {
  text-align: right;
}
</style>