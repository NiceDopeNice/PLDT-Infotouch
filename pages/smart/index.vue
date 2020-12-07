<template>
    <div class="product_page">

        <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'smart_footer'" :homeButton="true"  :backButton="true" :errorToggle="errorToggle" />

        <img class="product_logo" src="img/smart.png" alt="" />

          <transition name="fade">
            <section class="product_container" v-if="show">
              <div v-for="list in ButtonList" :key="list.btn_id" class="holder">
                <div class="button_container" v-if="list.btn_status == 1 && list.btn_category == 'smart'">
                  <button type="button" @click="PageHits(list.btn_id,list.btn_name,list.btn_class)">
                    <img :src="'img/button/'+list.btn_class+'.svg'" alt="">
                  </button>
                </div>
                <p v-if="list.btn_status == 1 && list.btn_category == 'smart'">{{ list.btn_name }}</p>
              </div>
            </section>
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
      errorToggle: false,
      ButtonList: [],
      CMS_URL_IMAGE: process.env.CMSUrl_image
    }
  },
  mounted() {
      this.$axios.$get('getButtonList').then( res => {
        if(res){
          this.show = true;
          this.loading_toggle = false;
          this.ButtonList = res;
        }
      }).catch( err => {
          console.log(err);
          this.errorToggle = true;
      });
  },
  methods:{
    PageHits(btn_id,btn_name,btn_url){
      this.loading_toggle = true;
      const me = this;
      const formdata = new FormData();

      if(btn_id == 9 || btn_id == 10 || btn_id == 11 || btn_id == 12 || btn_id == 15){
        formdata.append("p_category", 'SMART');
        formdata.append("p_district", '');
        formdata.append("p_ssc", '');
        formdata.append("p_ipaddress", localStorage.getItem("ip_address"));
        formdata.append("p_page", btn_name);
        formdata.append("p_feature", '');
        this.$axios.post('insertPageHits', formdata)
        .then(function (res) {
          if(res.status == 200){
            $nuxt.$router.push({ path: 'smart/'+btn_url });
          }else{
            me.errorToggle = true;
            console.log(error);
          }
        })
        .catch(function (error) {
          me.errorToggle = true;
          console.log(error);
        });
      }else{
        $nuxt.$router.push({ path: 'smart/'+btn_url });
      }

    }
  }
}


</script>