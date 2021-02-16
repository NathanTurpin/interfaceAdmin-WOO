<template>
  <div class="main">
      <button @click="addProduct"></button>
  </div>
</template>

<script>
import axios from 'axios'
export default {
    data(){
        return {
            token : localStorage.getItem("token")
        }
    },
methods: {
    addProduct() {
        axios.post("https://applicommande.local/wp-json/wc/v3/products",
        {
            name: "Premium Quality",
            type: "simple",
            regular_price: "21.99",
            description: "Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Vestibulum tortor quam, feugiat vitae, ultricies eget, tempor sit amet, ante. Donec eu libero sit amet quam egestas semper. Aenean ultricies mi vitae est. Mauris placerat eleifend leo.",
            short_description: "Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas.",
            categories: [
                {
                id: 9
                },
                {
                id: 14
                }
            ],
            images: [
                {
                src: "http://demo.woothemes.com/woocommerce/wp-content/uploads/sites/56/2013/06/T_2_front.jpg"
                },
                {
                src: "http://demo.woothemes.com/woocommerce/wp-content/uploads/sites/56/2013/06/T_2_back.jpg"
                }
            ]
        },{headers: { Authorization: "Bearer " + this.token }}
        )
    
    }    
}
}
</script>

<style>
</style>