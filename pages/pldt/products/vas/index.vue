<template>
  <div class="product_page">

    <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'pldt_footer'" :menuButton="true" :homeButton="true"  :backButton="true" :errorToggle="errorToggle" />

    <img class="product_logo" src="img/pldt.png" alt="" />

    <transition name="fade">
      <div class="vas_container" v-if="show">
        <div v-for="list in getPldtVas" :key="list.v_id">
            <div class="vas_box"  v-if="list.v_enable == 1">
              <button type="button" @click="PageHits(list.v_name,list.v_id)">
                {{ list.v_name }}
              </button>
            </div>
        </div>
      </div>
    </transition>

  </div>
</template>

<style scoped>
  .vas_box button{
    background: transparent;
    border: 1px solid transparent;
    outline: none;
    width: 100%;
    font-size: 1em;
    color: #7d7f81;
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
      getPldtVas: [],
      CMS_URL_IMAGE: process.env.CMSUrl_image
    }
  },
  mounted() {
      this.$axios.$get('getPldtVas').then( res => {
        if(res){
          this.show = true;
          this.loading_toggle = false;
          this.getPldtVas = res;
        }
      }).catch( err => {
          console.log(err);
          this.errorToggle = true;
      });
  },
  methods:{
    PageHits(v_name,v_id){
      this.loading_toggle = true;
      const me = this;
      const formdata = new FormData();

        formdata.append("p_category", 'PLDT');
        formdata.append("p_district", '');
        formdata.append("p_ssc", '');
        formdata.append("p_ipaddress", localStorage.getItem("ip_address"));
        formdata.append("p_page", 'PRODUCTS');
        formdata.append("p_feature", v_name);
        this.$axios.post('insertPageHits', formdata)
        .then(function (res) {
          if(res.status == 200){
            me.loading_toggle = false;
            $nuxt.$router.push({ path: 'vas/'+v_id });
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