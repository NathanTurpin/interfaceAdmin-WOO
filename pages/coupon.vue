<template>
  <div id="app">
    <div
      v-if="!registerActive"
    >
      <a href="#" @click="registerActive = !registerActive">
        Ajouter un coupon</a
      >
      <seecoupon />
    </div>

    <div v-else >
      <a href="#" @click="registerActive = !registerActive"
        >Voir les coupons</a
      >
      <addcoupon />
    </div>
  </div>
</template>

<script>
import seecoupon from "@/components/coupons/seecoupon";
import addcoupon from "@/components/coupons/addcoupon";

export default {
  components: {
    addcoupon,
    seecoupon,
  },
  data() {
    return {
      registerActive: false,

      token: "",
    };
  },
  mounted() {},
  methods: {},
};
</script>

<style>
</style>