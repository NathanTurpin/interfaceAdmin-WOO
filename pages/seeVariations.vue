<template>
  <div>
      <seeproduct   @see-variations="seeVariant"/>
      {{gg}}
  </div>
</template>

<script>
import seeproduct from "@/components/products/seeproduct"
export default {
    components:{
        seeproduct
    },
    data(){
        return{
            gg:""
        }
    },
    methods: {
        seeVariant(e){
           this.gg=e
        }
    }
}
</script>

<style>

</style>