<template>
    <div class="product_page">

        <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'pldt_footer'" :homeButton="true" :backButton="true" :menuButton="true"/>

        <img class="product_logo" src="img/pldt.png" alt="" />

          <transition name="fade">
            <section class="toggle_container" v-if="show">
                <div class="toggle_button">
                  <button @click="openContainer('pldt')" :class="{ active_container: pldt }" style="margin-right: 30px;">PLDT</button>
                  <button @click="openContainer('ultera')" :class="{ active_container: ultera }">ULTERA</button>
                </div>
 
                <div class="toggle_div" v-show="pldt">
                    <p class="title">Please enter the landline number of the bill you are requesting</p>
                    <p class="area_code">0 + AREA CODE + TELEPHONE NUMBER: </p>
                    <p class="area_code">[EXAMPLE: 02XXXXXXXX] </p>

                    <div class="area_code_main">
                      <nuxt-link to="/areacode">Get Area Code</nuxt-link>
                    </div>

                    <!-- <NumberCalculator :maxNumberInput="11"></NumberCalculator> -->
                    
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

                <div class="toggle_div" v-show="ultera">
                    <p class="title">Please enter the service number of the bill you are requesting</p>
                    <p class="area_code">0 + 10 DIGIT SERVICE NUMBER:  </p>
                    <p class="area_code">[EXAMPLE: 0XXXXXXXXXX] </p>
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
                                <button type="button" @click="Proceed(2)">PROCEED</button>
                            </div>
                        </div>
                    </div> 
                </div>

                <div class="billing_overlay" v-if="Invalid"></div>
                <div class="billing_invalid" v-if="Invalid">
                    <div @click="Invalid = false">x</div>
                    {{ ErrorText }}
                </div>

            </section>
          </transition>
    </div>
</template>

<script>
import axios from 'axios';
import Header from "~/components/Nuxt_header";
// import NumberCalculator from "~/components/Number_calculator";

export default {
  layout:'pldt',
  components: {
    Header
  },
  data(){
    return {
      show: false,
      loading_toggle: true,
      pldt: true,
      ultera: false,
      Asterisk: true,
      toggleImg: true,
      PhoneNumber: '',
      PhoneNumberAsterisk: '',
      Invalid: false, 
      ErrorText: ''
    }
  },
  mounted() {
      this.loading_toggle = false;
      this.show = true;
  },
  methods: {
    openContainer: function (value) {
      this.PhoneNumber = '';
      this.PhoneNumberAsterisk = '';
      if(value == 'pldt'){
        this.pldt = true;
        this.ultera = false;
      }else{
        this.ultera = true;
        this.pldt = false;
      }
    },
    turnToggle: function(){
        this.Asterisk = !this.Asterisk;
        this.toggleImg = !this.toggleImg;
    },
    numKey: function(num){
        if(num == 10){
            num = 0;
        }
        const number = this.PhoneNumber+''+num;

        if(this.PhoneNumber.length < 11){
            this.PhoneNumber = number;
            this.PhoneNumberAsterisk = number.replace(/./g, '*');
        }
    },
    backSpace: function(){
        this.PhoneNumber = this.PhoneNumber.slice(0,-1);
        this.PhoneNumberAsterisk = this.PhoneNumberAsterisk.slice(0,-1);
    },
    Proceed(toggle){
        if(toggle == 1){
          if(this.PhoneNumber.length == 0){
              this.Invalid = true;
              this.ErrorText = 'Please input telephone number';
          }else{
              this.Invalid = true;
              this.ErrorText = 'Invalid Telephone Number';
          }
        }else{
          if(this.PhoneNumber.length == 0){
              this.Invalid = true;
              this.ErrorText = 'Please input service number';
          }else{
              this.Invalid = true;
              this.ErrorText = 'Invalid Service Number';
          }
        }
    }
  }
}


</script>