<template>
    <div class="product_page">

        <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'smart_footer'" :homeButton="true" :backButton="true" :menuButton="true" />

        <img class="product_logo" src="img/smart.png" alt="" />

          <transition name="fade">
            <section class="toggle_container smartoverwrite" v-if="show">
                <div class="toggle_div" v-if="!ShowBilling">
                    <p class="title">CUSTOMER BILLING SUMMARY</p>
                    <p class="area_code">Please enter your 10-digit Smart Postpaid number  </p>
                    <p class="area_code">[EXAMPLE: 9981234567] </p>
                    <div class="margintop"></div>
                    
                    <div class="num_calculator">
                        <div class="search_remove">
                            <div class="number">
                                <span v-if="Asterisk">{{ PhoneNumberAsterisk }}</span>
                                <span v-else>{{ PhoneNumber }}</span>
                                <img @click="turnToggle" v-if="toggleImg" src="img/turnon.png" alt="">
                                <img @click="turnToggle" v-else src="img/turnoff.png" alt="">
                            </div>
                            <div class="backspace" @click="backSpace"><img src="img/backspace.svg" alt=""></div>
                        </div>
                        <div class="number_list">
                            <div v-for="n in 10" @click="numKey(n)" :key="n"><span v-if="n < 10">{{ n }}</span> <span v-else> 0</span></div>
                            <div class="proceed">
                                <button type="button" @click="Proceed(1)">PROCEED</button>
                            </div>
                        </div>
                    </div>

                </div>

                <div class="billing_details" v-else>
                    <p class="title">CUSTOMER BILLING SUMMARY</p>
                    <div class="billing_main" id="billing_main">
                      <p>Mobile Number: 09953006459</p>
                      <p>Balance from previous bill: PHP 3000.00</p>
                      <p>Current Balance: PHP 3000.00</p>
                      <p>Total Amount to pay: PHP 3000.00</p>
                      <p>Due date of payment: 3000.00</p>
                      <p>Minimum amount to pay: PHP 3000.00</p>
                      <p>Unbilled usages: PHP 3000.00</p>
                    </div>
                    <div class="billing_button">
                      <button type="button" @click="PrintBilling">Print</button>
                      <button type="button">View SOA</button>
                    </div>
                </div>
                
                <div class="billing_overlay" v-if="InvalidOverlay"></div>
                <div class="billing_invalid" v-if="Invalid">
                    <div @click="Invalid = false">x</div>
                    {{ ErrorText }}
                </div> 

                
                <div class="pin_overlay" v-if="OTPScreen"></div>
                <div class="otp_screen_main" v-if="OTPScreen">
                  <button type="button" @click="OTPScreen = !OTPScreen, Invalid = false" class="button-close">×</button>  
                  <div class="otp_screen">
                    <h2>PLEASE INPUT ONE TIME PIN NUMBER</h2>
                    <input type="text" class="otp_input" @click="OTPnumkey = true" :value="OTPnumber"/>
                      <div class="num_calculator" v-if="OTPnumkey">
                        <div class="number_list">
                            <div v-for="n in 9" @click="OTPnum(n)" :key="n"><span>{{ n }}</span></div>
                            <div @click="OTPbackSpace"><img src="img/backspace.svg" alt=""></div>
                            <div @click="OTPnum(0)"><span>0</span></div>
                            <div @click="OTPnumkey = !OTPnumkey"><img src="img/chevron-right.svg" alt=""></div>
                        </div>
                      </div>
                      <div class="otp_button">
                        <button type="button" @click="Proceed(2)">CONTINUE</button>
                      </div>
                      <div class="otp_button">
                        <p>Didn't receive the pin number?</p>
                        <button type="button" @click="Proceed(3)">RE-SEND PIN</button>
                      </div>
                  </div>
                </div>

            </section>
          </transition>
    </div>
</template>

<style scoped>

.toggle_div .title, .billing_details .title{
  font-size: 2.8em;
  margin-top: 5%;
  margin-bottom: 5%;
}
.toggle_div .area_code{
  font-weight: normal;
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
      ShowBilling: false,
      Asterisk: true,
      toggleImg: true,
      PhoneNumber: '',
      PhoneNumberAsterisk: '',
      Invalid: false, 
      InvalidOverlay: false,
      OTPnumber: '',
      OTPScreen: false, 
      OTPnumkey: false,
      ErrorText: ''
    }
  },
  mounted() {
      this.loading_toggle = false;
      this.show = true;
  },
  methods: { 
    turnToggle: function(){
      this.Asterisk = !this.Asterisk;
      this.toggleImg = !this.toggleImg;
    },
    numKey: function(num){
      if(num == 10){
          num = 0;
      }
      const number = this.PhoneNumber+''+num;

      if(this.PhoneNumber.length < 10){
          this.PhoneNumber = number;
          this.PhoneNumberAsterisk = number.replace(/./g, '*');
      }
    },
    backSpace: function(){
      this.PhoneNumber = this.PhoneNumber.slice(0,-1);
      this.PhoneNumberAsterisk = this.PhoneNumberAsterisk.slice(0,-1);
    },
    Proceed(toggle){
      this.loading_toggle = true;

      // VALIDATE PHONE NUMBER
      if(toggle == 1){
        setTimeout(() => {
          this.loading_toggle = false;
          if(this.PhoneNumber == ''){
              this.Invalid = true;
              this.ErrorText = 'Please input phone number.';            
          }
          else if(this.PhoneNumber == '0'){
              this.Invalid = true;
              this.ErrorText = 'Invalid phone number.';
          }
          else if(this.PhoneNumber.length < 10){
              this.Invalid = true;
              this.ErrorText = 'Phone number should be 10 digits.';
          }
          else if(this.PhoneNumber.substring(0,1) == '0'){
              this.Invalid = true;
              this.ErrorText = 'Please remove 0 as first digit of your number.';
          }else{
              // OTP SENDING
              this.OTPScreen = true;
          }
        }, 500);
      
      // PIN VALIDATION
      }else if(toggle == 2){
        setTimeout(() => {
          this.loading_toggle = false;
          const wrongPin = false;

          if(this.OTPnumber == ''){
            this.Invalid = true;
            this.ErrorText = 'The PinCode is required.';            
          }else if(wrongPin){
            this.Invalid = true;
            // Wrong PIN
            this.ErrorText = "Oops! You've entered the wrong PIN/SMS code. Please double check and try again.";   
            // Many Attempt
            // this.ErrorText = "Sorry, your account status is temporarily not allowed for this transaction.";   
          }else{
            // GET ACCOUNG BILLING SUMMARY
            this.ShowBilling = true;
            this.OTPScreen = false;
          }

        }, 500);
      
      // RE-SEND PIN
      }else if(toggle == 3){
        setTimeout(() => {
          this.loading_toggle = false;
          this.Invalid = false;
        }, 500);
      }

    },
    OTPnum: function(num){
      const number = this.OTPnumber+''+num;
      this.OTPnumber = number;
    },
    OTPbackSpace: function(){
      this.OTPnumber = this.OTPnumber.slice(0,-1);
    },
    PrintBilling(){
      const p = document.getElementById('billing_main');
      const WindowObject = window.open('', 'PrintWindow', 'width=750,height=650,top=50,left=50,toolbars=no,scrollbars=yes,status=no,resizable=yes');
      WindowObject.document.writeln('<p style="font-size:1px;">' + p.innerHTML + '</p>');
      WindowObject.document.close();
      WindowObject.focus();
      WindowObject.print();
      WindowObject.close();
      this.ShowBilling = false;
      this.PhoneNumber = '';
      this.PhoneNumberAsterisk = '';
    }
  }
} 


</script>