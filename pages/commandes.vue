<template>
  <div class="main">
    <p v-show="this.notif >= 0">{{ notif }}</p>
    <button @click="getProductImg">cc</button>
    {{ idProdImg }} <br />
    <br />
    <div v-for="(t,id) in test" :key="id">
      {{t}}
      <!-- <div v-for="x in t">
         {{x[id]}}
       <br><br>

      </div> -->

      <div v-for="commandes in ">

      </div>
    </div>
    <div class="table-responsive">
      <table class="table" v-for="commande in commandes">
        <thead>
          <tr>
            <th>{{ commande.id }}</th>
            <th>{{ commande.order_key }}</th>
            <th>{{ commande.date_created }}</th>
          </tr>
        </thead>
        <tbody v-for="(item,id) in commande.line_items" :key="id">
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

            <div
              class="tdImg"
              v-if="commande.line_items[id].id =item.id "
              v-show=" item.variation_id "
            >
            {{commande.line_items[id].id }}{{item.id}}
              <div v-for="variant in productsVariants" >
                <div v-for="vari in variant" >
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
              </div>
            </div>
            <div class="tdImg" v-if="commande.line_items[id].id ===item.id " v-show="!item.variation_id">
            {{commande.line_items[id].id }}{{item.id}}

              <div v-for="(product, id) in products">
                <div v-if="item.product_id === product.id">
                  <td>
                    <img
                      class="img-fluid"
                      v-if="product.images[0] !== undefined"
                      :src="product.images[0].src"
                      alt="Card image cap"
                    /> 
                  </td>
                </div>
              </div>
            </div>
          </tr>
        </tbody>
        <tfoot>
          ...
        </tfoot>
      </table>
    </div>
    <!-- {{ commande.status }} 
      {{ commande.total }} 
      {{ commande.billing }} -->
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
      notif: -1,
      test: [[]],
      commandes: [],
      lastCommandes: [],
      idProdImg: [],
      idProdVarImg: [],
      products: [],
      productsVariants: [],
      token: localStorage.getItem("token"),
    };
  },
  mounted() {
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
          (response) => (this.commandes = response.data)
          // console.log("actuel"),
          // console.log(this.commandes.length)
        )
        .catch((error) => console.log(error));

      if (this.commandes.length > this.lastCommandes.length) {
        this.notif += 1;
        this.getProductImg();
      }
      this.lastCommandes = this.commandes;

      // console.log("last:");
      // console.log(this.lastCommandes.length);
    },
    getProductImg() {
      this.idProdImg = [];
      this.test= [];

      for (let i = 0; i < this.commandes.length; i++) {
        for (let j = 0; j < this.commandes[i].line_items.length; j++) {
          this.idProdImg.push(this.commandes[i].line_items[j].product_id);
        }
      }

      this.idProdImg.forEach((el) =>
        axios
          .get(window.addresse + "/wp-json/wc/v3/products/" + el, {
            headers: {
              Authorization: "Bearer " + this.token,
            },
          })
          .then((response) => this.products.push(response.data))
          .catch((error) => console.log(error))
      );
      this.idProdImg.forEach((el) =>
        axios
          .get(
            window.addresse + "/wp-json/wc/v3/products/" + el + "/variations",
            {
              headers: {
                Authorization: "Bearer " + this.token,
              },
            }
          )
          .then((response) => this.productsVariants.push(response.data))
          .catch((error) => console.log(error))
      );
      this.test.push()
    },
  },
};
</script>

<style scoped>
.tdImg {
  max-width: 20%;
}
</style>