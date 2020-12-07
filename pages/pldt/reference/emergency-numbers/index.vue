<template>
  <div class="product_page">

    <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'pldt_footer'" :menuButton="true" :homeButton="true"  :backButton="true" :errorToggle="errorToggle" />
    <img class="product_logo" src="img/pldt.png" alt="" />

    <transition name="fade">
      <div class="g_container" v-if="show">
        <p class="title">EMERGENCY NUMBERS</p>
        <div class="pc_talbe_main">
            <table class="pc_alternate" >
                <thead>
                  <tr>
                    <td>Organization</td>
                    <td>Hotline Numbers</td>
                  </tr>
                </thead>
                <tr v-for="list in getHotlineList" :key="list.hot_id">
                  <td>{{ list.hot_org }}</td>
                  <td>{{ list.hot_no }}</td>
                </tr>
            </table>
        </div>
      </div>
    </transition>

  </div>
</template>

<style scoped >
    .g_container{
        opacity: 1;
    }
    .g_container .title{
        margin-bottom: 2%;
    }
    .pc_alternate{
      width: 100%;
      font-size: 1.6em;
    }
    .pc_alternate td{
      padding: 6px 10px;
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
      getHotlineList: []
    }
  },
  mounted() {
      this.$axios.$get('getPldtHotlines').then( res => {
        if(res){
          this.show = true;
          this.loading_toggle = false;
          this.getHotlineList = res;
        }
      }).catch( err => {
          console.log(err);
          this.errorToggle = true;
      });
  }
}


</script>