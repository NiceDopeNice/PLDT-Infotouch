<template>
  <div class="product_page">

    <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'pldt_footer'" :menuButton="true" :homeButton="true"  :backButton="true" :errorToggle="errorToggle" />
    <img class="product_logo" src="img/pldt.png" alt="" />

    <transition name="fade">
      <div class="g_container" v-if="show">
        <p class="title">{{ $route.query.channelname }}</p>
        <h2>{{ subtitle }}</h2>
        <div class="pc_talbe_main">
            <table class="pc_alternate" >
                <tr v-for="list in getPaymentChannelList" :key="list.pc_id"><td>{{ list.pc_name }}</td></tr>
            </table>
        </div>
      </div>
    </transition>

  </div>
</template>

<style scoped>
    .g_container .title{
        margin-bottom: 2%;
    }
</style>

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
      getPaymentChannelList: [],
      subtitle: ''
    }
  },
  mounted() {
      const slug_id = this.$route.params.slug;
      this.$axios.$get('getPldtPaymentChannelByType?pc_type='+slug_id).then( res => {
        if(res){
          this.show = true;
          this.loading_toggle = false;
          this.getPaymentChannelList = res;

            switch(parseInt(slug_id)) {
                case 1:
                    this.subtitle = 'You may settle your PLDT bills at any payment centers listed below';
                    break;
                case 2:
                    this.subtitle = 'You may pay your bills at any of these accredited banks below';
                    break;
                case 3:
                    this.subtitle = 'Pay your bills fast and easy. Just log on to these sites:';
                    break;
                case 4:
                    this.subtitle = 'Pay your bills on time and hassle-free! Visit your bank to enroll your credit card to our Auto- Debit Program';
                    break;
                case 5:
                    this.subtitle = 'Now you can pay your bills conveniently via ATM!';
                    break;
                case 6:
                    this.subtitle = 'Pay your bills on time at the comfort of your home or in your office with just one phone call';
                    break;
                default:
                    this.subtitle = '';
                    break;
             }
        }
      }).catch( err => {
          console.log(err);
          this.errorToggle = true;
      });
  }
}


</script>