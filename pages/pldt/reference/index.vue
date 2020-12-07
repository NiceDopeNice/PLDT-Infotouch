<template>
  <div class="product_page">

    <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'pldt_footer'" :menuButton="true" :homeButton="true"  :backButton="true" />
    <img class="product_logo" src="img/pldt.png" alt="" />

    <transition name="fade">
      <div class="vas_container" v-if="show">
        <div class="vas_box" v-for="list in referenceList" :key="list.name">

            <button type="button" @click="PageHits(list.report_name,list.url)" v-if="list.url == 'philippine-area-codes'">
              {{ list.name }}
            </button>

            <button type="button" @click="PageHits(list.report_name,list.url)" v-else-if="list.url == 'philippine-zip-codes'">
              {{ list.name }}
            </button>

            <button type="button" @click="PageHits(list.report_name,list.url)" v-else>
              {{ list.name }}
            </button>
        </div>
      </div>
    </transition>

  </div>
</template>

<style scoped>
.vas_container{
    width: auto;
}
.vas_box{
    border: 7px solid #b3b5b5;
    height: 265px;
    vertical-align: middle;
    line-height: 240px;
    border-radius: 30px;
    width: 850px;
    margin: 0 auto;
    margin-top: 2%;
    padding: 0px;
    opacity: 0.8;
    font-size: 4.3em;
    box-shadow: 0.1em 0.1em 1em rgba(0, 0, 0, 0.18);
}
.vas_box button{
  background: transparent;
  border: 1px solid transparent;
  outline: none;
  width: 100%;
  font-size: 1em;
  color: #7d7f81;
}
.vas_box a{
    display: block;
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
      referenceList: [
        {name: 'Payment Channels', report_name: 'Payment Channels', url: 'payment-channels'},
        {name: 'Philippine Area Codes', report_name: 'Area Codes', url: 'philippine-area-codes'},
        {name: 'Philippine Zip Codes', report_name: 'Zip Codes', url: 'philippine-zip-codes'},
        {name: 'Emergency Numbers', report_name: 'Emergency Numbers', url: 'emergency-numbers'}
      ]
    }
  },
  mounted() {
    this.show = true;
    this.loading_toggle = false;
  },
  methods:{
    PageHits(name,url){
      this.loading_toggle = true;
      const me = this;
      const formdata = new FormData();

      formdata.append("p_category", 'PLDT');
      formdata.append("p_district", '');
      formdata.append("p_ssc", '');
      formdata.append("p_ipaddress", localStorage.getItem("ip_address"));
      formdata.append("p_page", 'REFERENCES');
      formdata.append("p_feature", name);
      this.$axios.post('insertPageHits', formdata)
      .then(function (res) {
        if(res.status == 200){
          if(name == 'Payment Channels' || name == 'Emergency Numbers'){
            $nuxt.$router.push({ path: 'reference/'+url });
          }else if(name == 'Zip Codes'){
            $nuxt.$router.push({ path: '/areacode?zipcode=1' });
          }else if(name == 'Area Codes'){
            $nuxt.$router.push({ path: '/areacode' });
          }
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