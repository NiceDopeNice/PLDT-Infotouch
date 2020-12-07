<template>
  <div class="product_page">

    <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'smart_footer'" :menuButton="true" :homeButton="true"  :backButton="true" :errorToggle="errorToggle"  />

    <img class="product_logo" src="img/smart.png" alt="" />
    <h1 class="inside_title smart_title">SELECT A PRODUCT</h1>

    <transition name="fade">
      <div class="pldt_product_container smart_prod_container" v-if="show">
        <div v-for="list in getSmartProducts" :key="list.h_id">
          <div class="product_box" v-if="list.h_enable == 1">
            <button type="button" @click="PageHits(list.h_id,list.h_name)">
                <img :src="CMS_URL_IMAGE+''+list.h_logo" alt="">
            </button>
          </div>
        </div>
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
      show: false,
      loading_toggle: true,
      getSmartProducts: [],
      errorToggle: false,
      CMS_URL_IMAGE: process.env.CMSUrl_image
    }
  },
  mounted() {
      this.$axios.$get('getSmartProducts').then( res => {
        if(res){
          this.show = true;
          this.loading_toggle = false;
          this.getSmartProducts = res;
        }
      }).catch( err => {
          console.log(err);
          this.errorToggle = true;
      });
  },
  methods:{
    PageHits(h_id,h_name){
      this.loading_toggle = true;
      const me = this;
      const formdata = new FormData();

      formdata.append("p_category", 'SMART');
      formdata.append("p_district", '');
      formdata.append("p_ssc", '');
      formdata.append("p_ipaddress", localStorage.getItem("ip_address"));
      formdata.append("p_page", 'PRODUCTS');
      formdata.append("p_feature", h_name);
      this.$axios.post('insertPageHits', formdata)
      .then(function (res) {
        if(res.status == 200){
          $nuxt.$router.push({ path: 'products/browse/'+h_id });
        }else{
          me.errorToggle = true;
          console.log(error);
        }
      })
      .catch(function (error) {
        me.errorToggle = true;
        console.log(error);
      });

    }
  }
}


</script>