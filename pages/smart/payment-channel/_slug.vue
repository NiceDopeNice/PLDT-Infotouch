<template>
  <div class="product_page">

    <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'smart_footer'" :menuButton="true" :homeButton="true"  :backButton="true" />
    <img class="product_logo" src="img/smart.png" alt="" />

    <transition name="fade">
      <div class="g_container smartoverwrite" v-if="show">
        <p class="title">{{ ChannelName }}</p>
        <h2>{{ subtitle_top }}</h2>
        <div class="pc_talbe_main">
            <!-- Single TD -->
            <table class="pc_alternate" v-if="SlugID == 9 || SlugID == 12">
                <tr v-for="list in getPaymentChannelList" :key="list.pc_id"><td>{{ list.pc_name }}</td></tr>
            </table>
            <!-- Double TD -->
            <table class="pc_alternate addpadd" v-if="SlugID == 10 || SlugID == 11">
                <tr v-for="list in getPaymentChannelList" :key="list.pc_id">
                  <td>{{ list.pc_name }}</td>
                  <td>{{ list.pc_other }}</td>
                </tr>
            </table>
            <!-- For Others -->
             
            <div class="smart_other_main" v-if="SlugID == 13">
              <div class="smart_other" v-for="list in getPaymentChannelList" :key="list.pc_id">
                <div class="other_title">{{ list.pc_name }}</div>
                <div class="other_details">{{ list.pc_other }}</div>
                <hr />
              </div>
            </div>
            
        </div>
        <h2 class="sub_bottom" v-if="subtitle_bottom">{{ subtitle_bottom }}</h2>
        <div class="mobile_banking_instruc" v-if="mobilebank">
          <table>
            <tr>
              <td class="num">1. </td>
              <td> Select Mobile Banking menu in your Smart cellphone SIM Menu</td>
            </tr>
            <tr>
              <td class="num">2. </td>
              <td> Click Pay Bills and enter 03006 (Smart Biller Code) or 06012 (Smart Bro Biller Code), your Smart account number and payment amount.</td>
            </tr>
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
  layout:'smart',
  components: {
    Header
  },
  data(){
    return {
      show: false,
      loading_toggle: true,
      getPaymentChannelList: [],
      subtitle_top: '',
      subtitle_bottom: '',
      mobilebank: false,
      ChannelName: this.$route.query.channelname.toUpperCase(),
      SlugID: this.$route.params.slug
    }
  },
  mounted() {
      const ChannelName = this.$route.query.channelname;
      const slug_id = this.$route.params.slug;
      this.show = true;
      this.loading_toggle = false;
      switch(parseInt(slug_id)) {
          case 9:
              this.mobilebank = true;
              this.subtitle_top = 'You may pay via Mobile Banking Service (MBS) through the following banks:';
              this.subtitle_bottom = 'To use the service, inquire and fill-out an MBS application form at these banks or call our toll free hotline number 15177 using your Smart cellphone or 8457777 using your landline to inquire about this payment facility. Once enrolled, simply follow these steps:';
              this.getPaymentChannelList = [
                  {pc_id:1,pc_name:'Asia United Bank'},{pc_id:2,pc_name:'BDO'},{pc_id:3,pc_name:'BPI'},{pc_id:4,pc_name:'Chinatrust Savings Bank'},{pc_id:5,pc_name:'Citystate Savings Bank'},{pc_id:6,pc_name:'Development Bank of the Philippines'},{pc_id:7,pc_name:'EastWest Bank'},{pc_id:8,pc_name:'Metrobank'},{pc_id:9,pc_name:'RCBC'},{pc_id:11,pc_name:'Security Bank'},{pc_id:12,pc_name:'Sterling Bank'},{pc_id:13,pc_name:'Union Bank'},{pc_id:14,pc_name:'UCPB'}
                ]
              break;
          case 10:
              this.mobilebank = false;
              this.subtitle_top = 'Pay your bills fast and easy. Just log on to these sites:';
              this.subtitle_bottom = 'You may be required to register for an Internet banking account with your bank.';
              this.getPaymentChannelList = [
                  {pc_id:1,pc_name:'AUB',pc_other:'https://www.aub.com.ph'},{pc_id:2,pc_name:'BancNet',pc_other:'http://www.bancnetonline.com'},{pc_id:3,pc_name:'BDO',pc_other:'https://www.mybdo.com.ph'},{pc_id:4,pc_name:'BPI',pc_other:'http://www.bpiexpressonline.com'},{pc_id:5,pc_name:'EastWest',pc_other:'http://www.eastwestbanker.com'},{pc_id:6,pc_name:'HSBC',pc_other:'http://www.hsbc.com.ph'},{pc_id:7,pc_name:'Maybank',pc_other:'http://www.maybank2u.com'},{pc_id:8,pc_name:'Metrobank',pc_other:'https://www.aub.com.ph'},{pc_id:9,pc_name:'AUB',pc_other:'http://www.metrobank.com.ph'},{pc_id:10,pc_name:'PNB',pc_other:'http://www.pnb.com.ph'},{pc_id:11,pc_name:'PSBank',pc_other:'http://www.psbank.com.ph'},{pc_id:12,pc_name:'Security Bank',pc_other:'http://www.securitybank.com'},{pc_id:13,pc_name:'UCPB',pc_other:'https://www.ucpb.com'},{pc_id:14,pc_name:'Union Bank',pc_other:'http://www.unionbankph.com'}
                ]
              break;
          case 11:
              this.mobilebank = false;
              this.subtitle_top = "Upon enrollment of this service at your depository bank, you may conveniently pay your bills by calling your bank's number:";
              this.getPaymentChannelList = [
                  {pc_id:1,pc_name:'BPI Express Phone',pc_other:'89-100'},{pc_id:2,pc_name:'BDO Phonebanking',pc_other:'631-8000 (Metro Manila), 1-800-10-631-8000 (domestic toll-free)'},{pc_id:3,pc_name:'HSBC Phonebanking',pc_other:'85-800'},{pc_id:4,pc_name:'Landbank Phone Access',pc_other:'405-7000'},{pc_id:5,pc_name:'Metrobank Metrophone',pc_other:'5-8000'},{pc_id:6,pc_name:'United Coconut Planters Bank',pc_other:'811-9999 (Metro Manila), 1-800-1-888-8272 (domestic toll-free)'}
                ]
              break;
          case 12:
              this.mobilebank = false;
              this.subtitle_top = "You may pay your bills at any of these accredited banks below:";
              this.subtitle_bottom = '*For Development Bank of the Philippines (DBP), bills payments will be accepted in the online branches of the bank.';
              this.getPaymentChannelList = [
                  {pc_id:1,pc_name:'Asia United Bank'},{pc_id:2,pc_name:'Banco de Oro (BDO)'},{pc_id:3,pc_name:'Bank of Commerce'},{pc_id:4,pc_name:'Bank of Philippine Islands (BPI)'},{pc_id:5,pc_name:'Development Bank of the Philippines*'},{pc_id:6,pc_name:'East West Bank'},{pc_id:7,pc_name:'Luzon Development Bank'},{pc_id:8,pc_name:'Malayan Bank'},{pc_id:9,pc_name:'Maybank'},{pc_id:10,pc_name:'Metrobank'},{pc_id:11,pc_name:'Philippine National Bank (PNB)'},{pc_id:12,pc_name:'Planters Bank'},{pc_id:13,pc_name:'RCBC'},{pc_id:14,pc_name:'RCBC Savings Bank'},{pc_id:15,pc_name:'Robinsons Bank'},{pc_id:16,pc_name:'Security Bank'},{pc_id:17,pc_name:'Union Bank of the Philippines'},{pc_id:18,pc_name:'United Coconut Planters Bank'}
                ]
              break;
          case 13:
              this.mobilebank = false;
              this.getPaymentChannelList = [
                  {pc_id:1,pc_name:'ATM Payments',pc_other:'To pay your bill through BANCNET, BDO and BPI EXPRESS ATMs , simply go to Bills Payment, select SMART or SMART / SUN, and follow instructions as prompted.'},{pc_id:2,pc_name:'Bayad Centers',pc_other:'Paying your bills is easy with over 3,500 accredited Bayad Center outlets nationwide. Check payment is accepted at selected branches only.'},{pc_id:3,pc_name:'Cebuana Lhuillier',pc_other:'Payments may be accepted at any of the 1,500 Cebuana Lhuillier outlets.'},{pc_id:4,pc_name:'M Lhuillier',pc_other:'Payments may be accepted at any of the 2,000 M Lhuillier outlets.'},{pc_id:5,pc_name:'Robinsons Department Store',pc_other:'Smart bill payments are accepted at all Robinsons Department Stores.'},{pc_id:6,pc_name:'SM and Savemore',pc_other:'Smart bill payments are accepted at all SM Malls and selected Savemore branches.'},{pc_id:7,pc_name:'7-Eleven Stores and ECPay Partner Merchants',pc_other:'You may pay at any of the over 1300 7-Eleven outlets and 5000 ECPay Partner Merchants nationwide. Selected 7-Eleven stores are open 24 hours a day, seven days a week.'}
                ]
              break;
      }
  }
}


</script>