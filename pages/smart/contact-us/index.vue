<template>
  <div class="product_page">

    <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'smart_footer'" :menuButton="true" :homeButton="true"  :backButton="true":errorToggle="errorToggle"  />
    <img class="product_logo" src="img/smart.png" alt="" />

    <transition name="fade">
      <div class="g_container net_finder contactUs" v-if="show">
        <p class="title">CALL US</p>
        <img src="img/contact-us.svg" alt="">
        <div class="contact_header">
            <div>SERVICE</div>
            <div>TOLL-FREE VIA SMART SIM</div>
            <div>VIA HOTLINE</div>
            <hr />
        </div>

        <div class="contact_list" v-for="list in getSmartContact" :key="list.c_id">
            <div>{{ list.c_service }}</div>
            <div>{{ list.c_toll }}</div>
            <div>{{ list.c_hotline }}</div>
        </div>
        <p class="contact_toll">Toll-free charging is not applicable during international roaming.</p>
      </div>
    </transition>
 
  </div>
</template>

<script>
import axios from 'axios';
import Header from "~/components/Nuxt_header";

export default {
  layout:'smart',
  components: {
    Header
  },
  data(){
    return {
      loading_toggle: true,
      show: false,
      errorToggle: false,
      getSmartContact: []
    }
  },
  mounted() {
      this.$axios.$get('getSmartContact').then( res => {
        if(res){
          this.show = true;
          this.loading_toggle = false;
          this.getSmartContact = res;
        }
      }).catch( err => {
          console.log(err);
          this.errorToggle = true;
      });
  }
}


</script>