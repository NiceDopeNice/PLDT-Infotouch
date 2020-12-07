<template>
  <div class="product_page">

    <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'pldt_footer'" :menuButton="true" :homeButton="true"  :backButton="true" :errorToggle="errorToggle" />
    <img class="product_logo" src="img/pldt.png" alt="" />

    <transition name="fade">
      <div class="g_container" v-if="show">
        <p class="title">SELECT A PAYMENT CHANNEL</p>
          <nuxt-link v-for="list in getPaymentChannel" :key="list.pct_id"  :to="'payment-channels/'+list.pct_id+'?channelname='+list.pct_type">
              {{ list.pct_type }}
          </nuxt-link>
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
      getPaymentChannel: []
    }
  },
  mounted() {
      this.$axios.$get('getPldtTypePaymentChannelByBrand?pct_group=pldt').then( res => {
        if(res){
          this.show = true;
          this.loading_toggle = false;
          this.getPaymentChannel = res;
        }
      }).catch( err => {
          console.log(err);
          this.errorToggle = true;
      });
  }
}


</script>