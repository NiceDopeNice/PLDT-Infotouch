<template>
  <div class="product_page">

    <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'pldt_footer'" :menuButton="true" :homeButton="true"  :backButton="true" :errorToggle="errorToggle" />

    <img class="product_logo" src="img/pldt.png" alt="" />

    <transition name="fade">
      <div class="vas_iframe_container" v-if="show">
          <div class="vas_iframe">
              <iframe :src="PldtProduct[0].h_link" frameborder="0"></iframe>
          </div>

      </div>
    </transition>

  </div>
</template>

<script>
import axios from 'axios';
import Header from "~/components/Nuxt_header";

export default {
  layout:'pldt',
  components: {
    Header
  },
  data(){
    return {
      show: false,
      loading_toggle: true,
      errorToggle: false,
      PldtProduct: []
    }
  },
  mounted() {
      this.$axios.$get('getPldtProductByID?h_id='+this.$route.params.slug).then( res => {
        if(res){
          this.show = true;
          this.loading_toggle = false;
          this.PldtProduct = res;
        }
      }).catch( err => {
          console.log(err);
          this.errorToggle = true;
      });
  }
}


</script>