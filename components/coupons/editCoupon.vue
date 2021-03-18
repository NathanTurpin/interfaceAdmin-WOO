<template>
  <div>
      <b-modal id="modal1" ref="modal1" :title="coupon.code">
        {{coupon}}
    </b-modal>
  </div>
</template>

<script>
export default {
    name:'editCoupon',
    props:["idEditCoupon","coupon"],
    methods: {
      show() {
                this.$refs.modal1.show();
            }
    }

}
</script>

<style>

</style>