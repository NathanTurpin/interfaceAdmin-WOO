<template>
  <div class="main">
    <div>
      <div v-for="(commande, idCommande) in filteredList" :key="idCommande">
        <table
          class="table table-bordered"
          v-if="commande.status != 'completed'"
        >
          <thead v-if="commande" @click="showAllOrder(idCommande)">
            <tr>
              <th class="table-dark titreCommande" scope="col">
                <h3>{{ commande.id }}</h3>
                <h5>
                  {{ commande.order_key }} |
                  {{ commande.date_created.replace("T", " ") }}
                </h5>
                <h5>{{ commande.total }} €</h5>
              </th>
            </tr>
          </thead>
          <div>
          <tbody
            v-show="showAll && idTab === idCommande"
            class="tdBody"
            v-for="(item, id) in commande.line_items"
            :key="id"
          >
            <tr class="table-info" v-if="item.variation_id != 0">
              <td v-if="id === 0">ID_variant</td>
              <td v-if="id === 0">Nom_variant</td>
              <td v-if="id === 0">Quantité</td>
              <td v-if="id === 0">Prix unitaire</td>
              <td v-if="id === 0">Prix total HT</td>
              <td v-if="id === 0">Image produit</td>
            </tr>
            <tr v-if="item.variation_id != 0">
              <td>{{ item.variation_id }}</td>
              <td class="tdNameVar">{{ item.name }}</td>
              <td>{{ item.quantity }}</td>
              <td>{{ item.price }} €</td>
              <td>{{ item.subtotal }} €</td>
              <div v-if="listeCommande[idCommande] != undefined">
                <td
                  class="tdImg"
                  v-if="listeCommande[idCommande][id] !== undefined"
                >
                  <img
                    class="img-fluid"
                    v-if="listeCommande[idCommande][id].image !== undefined"
                    :src="listeCommande[idCommande][id].image.src"
                    alt="Card image cap"
                  />
                </td>
              </div>
              <div v-else><img src="../static/loader.gif" /></div>
            </tr>
            <tr class="table-info" v-if="item.variation_id === 0">
              <td v-if="id === 0">ID_produit</td>
              <td v-if="id === 0">Nom</td>
              <td v-if="id === 0">Quantité</td>
              <td v-if="id === 0">Prix unitaire</td>
              <td v-if="id === 0">Prix total HT</td>
              <td v-if="id === 0">Image produit</td>
            </tr>
            <tr v-if="item.variation_id === 0">
              <td>{{ item.product_id }}</td>
              <td>{{ item.name }}</td>
              <td>{{ item.quantity }}</td>
              <td>{{ item.price }} €</td>
              <td>{{ item.subtotal }} €</td>
              <div v-if="listeCommande[idCommande] != undefined">
                <td
                  class="tdImg"
                  v-if="listeCommande[idCommande][id] !== undefined"
                >
                  <img
                    class="img-fluid"
                    v-if="listeCommande[idCommande][id].images !== undefined"
                    :src="listeCommande[idCommande][id].images[0].src"
                    alt="Card image cap"
                  />
                </td>
              </div>
              <div v-else><img src="../static/loader.gif" /></div>
            </tr>
          </tbody>
          </div>
          <tfoot v-show="showAll && idTab === idCommande">
            <div v-if="commande.coupon_lines.length !== 0">
              {{ commande.coupon_lines[0].code }}
              {{ commande.coupon_lines[0].discount }}€
            </div>
            <tr>
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

            <button
              type="button"
              class="btn btn-danger trFooterSelectBtn"
              @click="editOrders(commande.id)"
            >
              Valider
            </button>
            <tr class="trFooterSelect">
              <select v-model="form.statusEdit">
                <option disabled value="">{{ commande.status }}</option>
                <option>completed</option>
              </select>
              <span>Selected: {{ form.statusEdit }}</span>
            </tr>
          </tfoot>
        </table>
      </div>
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
      form: {
        statusEdit: "",
      },
      showAll: false,
      idTab: null,
      choixOption: "",

      ready: 0,
      commandes: [],
      commandesLire: [],
      lastCommandes: [],
      token: localStorage.getItem("token"),
      listeCommande: [],
      listeCommandeLire: [],
      adresse: window.addresse + "/assets/loader.gif",
    };
  },
  computed: {
    filteredList() {
      try {
        return this.commandes.filter((commande) => {
          if (this.choixOption != "completed") {
            return this.commandes;
          } else
            return commande.status.toLowerCase().includes(this.choixOption);
        });
      } catch (error) {
        this.getCommandes();
        // console.log(error)
        return 0;
      }
    },
  },
  mounted() {
    var self = this;
    self.getCommandes();
    setInterval(function () {
      self.getCommandes();
    }, 40000);
  },
  methods: {
    changeType(event) {
      this.choixOption = event.target.value;
      // this.selectedCountry = event.target.options[event.target.options.selectedIndex].text
    },
    async getCommandes() {
      await axios
        .get(window.addresse + "wp-json/wc/v3/orders", {
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then((response) => (this.commandesLire = response.data))
        .catch((error) => console.log(error));

      this.commandes = this.commandesLire;

      // console.log(this.commandesLire)
      if (this.commandesLire.length > this.lastCommandes.length) {
        this.ready = 0;
        for (let i = 0; i < this.commandesLire.length; i++) {
          // pour chaque commande on crée un tableau des produits commandés
          var produit = [];
          for (let j = 0; j < this.commandesLire[i].line_items.length; j++) {
            // pour chaque ligne dans une commande on recupère les infos du produits commandé
            if (
              this.commandesLire[i].line_items[j].product_id !== "undefined"
            ) {
              await axios
                .get(
                  window.addresse +
                    "/wp-json/wc/v3/products/" +
                    this.commandesLire[i].line_items[j].product_id,
                  {
                    headers: {
                      Authorization: "Bearer " + this.token,
                    },
                  }
                )
                .then((response) => (produit[j] = response.data)) // on met le produit trouvé dans le tableau
                .catch((error) => console.log("toto"));

              if (this.commandesLire[i].line_items[j].variation_id) {
                // si le produit est un variant on va chercher la variation du produit
                await axios
                  .get(
                    window.addresse +
                      "/wp-json/wc/v3/products/" +
                      produit[j].id +
                      "/variations/" +
                      this.commandesLire[i].line_items[j].variation_id,
                    {
                      headers: {
                        Authorization: "Bearer " + this.token,
                      },
                    }
                  )
                  .then((response) => (produit[j] = response.data)) // on met le produit variant trouvé dans le tableau
                  .catch((error) => console.log(error));
              }
            }
          }

          this.listeCommandeLire[i] = produit; // le tableau des produits de la commande est placé dans le tableau des commandes
          this.listeCommande = this.listeCommandeLire;
        }
        this.lastCommandes = this.commandesLire;
      }
      this.commandes = this.commandesLire;
      this.listeCommande = this.listeCommandeLire;
      this.ready = 1;
      //   this.test(this.date);
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
        .then(
          (res) => console.log(res),
          console.log("modif ok"),
          this.getCommandes()
        )
        .catch((error) => console.log(error));
    },
    showAllOrder(id) {
      this.showAll = !this.showAll;
      this.idTab = id;
    },
  },
};
</script>

<style scoped>
table {
  width: 70%;
  margin: auto;
  margin-bottom: 2%;
}
thead,
tfoot {
  width: 100%;
}
.titreCommande {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}
.titreCommande h5 {
  padding-top: 0.5%;
}
.tdBody td {
  width: 23%;
}
.tdFooter {
  display: flex;
  justify-content: space-between;
}
.infoClient1 {
  margin-bottom: -2%;
}
.trFooterSelect {
  float: right;
}
.trFooterSelectBtn {
  float: right;
  margin-top: 0.5%;
}
</style>