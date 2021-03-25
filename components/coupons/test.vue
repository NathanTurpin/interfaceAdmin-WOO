<template>
  <b-modal
    id="modal-scrollable modal-xl"
    scrollable
    size="xl"
    ref="modal1"
    :title="coupon.code"
  >
    <!-- // Categories POUR LE COUPON -->
    <div>
    <label class="col-sm-4 col-form-label" for="">
      Categories de produits :
    </label>
    <div class="col-sm-8">
      <select v-model="selectedCat" @change="pushCat(selectedCat)">
        <option v-for="(cat, key) in categories" :value="key" :key="key">
          {{ cat.name }}
        </option>
      </select>
    </div>
    <br />
    <p>Selectionnés:</p>
    <span
      v-for="(cat, index) in categorieCoupon"
      :key="index"
      @click="suppCatCoupon(index)"
    >
      {{ cat.name }}</span
    >
    </div>
    <br />
    <br />

    <!-- // categories exclu POUR LE COUPON -->
    <div>
    <label class="col-sm-4 col-form-label" for="">
      Categories exclu de produits :
    </label>
    <div class="col-sm-8">
      <select
        v-model="selectedCatExclu"
        @change="pushCatExclu(selectedCatExclu)"
      >
        <option v-for="(cate, key) in categories" :value="key" :key="key">
          {{ cate.name }}
        </option>
      </select>
    </div>
    <br />
    <p>Selectionnés:</p>
    <span
      v-for="(cate, i) in categorieCouponExclu"
      :key="i"
      @click="suppCatCouponExclu(i)"
    >
      {{ cate.name }}</span
    >
    </div>
    <br />
    <br />
    <button @click="editCoupon()">Valider</button>
  </b-modal>
</template>

<script>
import axios from "axios";
export default {
  name: "test",
  props: ["coupon", "categories", "categorieCoupon", "categorieCouponExclu"],
  data() {
    return {
      token: localStorage.getItem("token"),
      selectedCat: "",
      lastTabCatID: [],
      lastTabCatExcluID: [],

      selectedCatExclu: "",
    };
  },
  mounted() {},
  methods: {
    show() {
      this.$refs.modal1.show();
    },

    pushCat(selectedCat) {
      this.categorieCoupon.push(this.categories[selectedCat]);
      console.log(this.categorieCoupon);
    },
    suppCatCoupon(index) {
      this.categorieCoupon.splice(index, 1);
    },
    pushCatExclu(selectedCatExclu) {
      this.categorieCouponExclu.push(this.categories[selectedCatExclu]);
      console.log(this.categorieCouponExclu);
    },
    suppCatCouponExclu(i) {
      this.categorieCouponExclu.splice(i, 1);
    },
    editCoupon() {
      console.log("cat Coupon");
      this.lastTabCatID = [];
      this.lastTabCatExcluID = [];
      for (let i = 0; i < this.categorieCoupon.length; i++) {
        this.lastTabCatID.push(this.categorieCoupon[i].id);
      }
      console.log(this.lastTabCatID);
      console.log("cat exclu Coupon");

      for (let i = 0; i < this.categorieCouponExclu.length; i++) {
        this.lastTabCatExcluID.push(this.categorieCouponExclu[i].id);
      }
      console.log(this.lastTabCatExcluID);
    },
  },
};
</script>

<style>
</style>