<template>
  <div>
      <input type="text" v-model="nameEdit" placeholder="nom">
      <input type="number" v-model="priceEdit" placeholder="€">
      <button  @click="editProduct()">valider</button>
  </div>
</template>

<script>
import axios from "axios"
export default {
name:"editProduct",
props:["idEditProduct"],
data(){
    return{
        nameEdit:"",
        priceEdit: null,
        token: localStorage.getItem('token')
    }
},
methods: {
editProduct() {
      alert(this.idEditProduct);
      const url = "http://applicommande.local//wp-json/wc/v3/products/" + this.idEditProduct;
      axios
        .put(url,{
            name: this.nameEdit,
            regular_price: this.priceEdit
        }, {
          headers: {
            Authorization: "Bearer " + this.token,
          },
        })
        .then((res) => console.log(res))
        .catch((error) => {
          console.log(error);
        });
    },
}
}
</script>

<style>

</style>