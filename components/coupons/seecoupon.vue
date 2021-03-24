<template>
  <div class="container-fluid">
    <div class="search-wrapper">
      <label>Search title:</label>

      <input type="text" v-model="search" placeholder="Search title.." />
    </div>
    <div class="row row-cols-1 row-cols-md-5 m-l-2">
      <div
        class="card"
        v-for="(coupon, idCoupon) in filteredList"
        :key="idCoupon"
      >
        <div class="card-body">
          <h5 class="card-title">{{ coupon.code }}</h5>
          {{ coupon.id }}

          <br /><br />
          <button class="btn btn-danger" @click="deleteProduct(coupon.id)">
            supp
          </button>
          <!-- <button
            class="btn btn-info"
            @click="showEditComponent(coupon.id, idCoupon)"
          >
            edit
          </button> -->
          <!-- <div v-show="showEdit && idTab === idCoupon">

          <editCoupon
            :coupon="coupon"
            :coupon="coupon"
          />
         </div> -->
          <!-- 
          <button
            @click="showEditComponent(coupon, idCoupon)"
            class="btn btn-light"
          >
            ok
          </button> -->
          <button
            v-b-modal.modal-scrollable
            v-b-modal.modal-xl
            @click="showModal(coupon, idCoupon)"
          >
            ShowModal
          </button>
        </div>
      </div>
    </div>

    <editCoupon
      :coupon="coupon"
      :productCoupon="productCoupon"
      ref="modalComponent"
      @clicked="onClickChild"
    />
     <!-- <test
      :coupon="coupon"
      :productCoupon="productCoupon"
      ref="modalComponent"
    /> -->
  </div>
</template>

<script>
import axios from "axios";
import editCoupon from "@/components/coupons/editCoupon";
import test from "@/components/coupons/test";

export default {
  components: {
    editCoupon,
    test
  },
  data() {
    return {
      search: "",
      coupons: [],
      token: localStorage.getItem("token"),
      showEdit: false,
      coupon: [],
      componentEditCoupon: false,
      productCoupon: [],
      idTab: null,
    };
  },
  mounted() {
    this.getCoupons();
  },
  computed: {
    filteredList() {
      try {
        return this.coupons.filter((coupon) => {
          return coupon.code.toLowerCase().includes(this.search.toLowerCase());
        });
      } catch (error) {
        this.getCoupons();
        return 0;
      }
    },
  },
  methods: {
    onClickChild (value) {
      this.getCoupons()
    },
    // AFFICHE LES PRODUITS
    getCoupons() {
      axios
        .get(window.addresse + "wp-json/wc/v3/coupons", {
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then(
          (response) => (
            (this.coupons = response.data), console.log(this.coupons)
          )
        )
        .catch((error) => console.log(error));
    },
    // DELETE PRODUCT
    deleteProduct(idP) {
      alert(idP);
      const url = window.addresse + "wp-json/wc/v3/coupons/" + idP;
      axios
        .delete(url, {
          data: {
            force: true,
          },
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then((res) => console.log(res), this.getCoupons())
        .catch((error) => {
          console.log(error);
        });
    },
    // EDIT PRODUCT
    showModal(coupon, idProduct) {
      this.productCoupon = [];
      this.$refs.modalComponent.show();
      this.coupon = coupon;
      this.idTab = idProduct;
      console.log();
      for (let i = 0; i < this.coupon.product_ids.length; i++) {
        axios
          .get(window.addresse + "wp-json/wc/v3/products/" + this.coupon.product_ids[i], {
            headers: {
              Authorization: "Bearer " + this.token,
            },
          })
          .then(
            (response) => (
              this.productCoupon.push(response.data),
              console.log(this.productCoupon)
            )
          )
          .catch((error) => console.log(error));
      }
    
    },
  },
};
</script>

<style scoped>
.card {
  margin: 1%;
}
.card-body {
  padding: inherit;
  margin: 0% 0% 2% -2%;
}
.btn-light {
}
.btn {
  margin: 1%;
}
</style>
