<template>
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
              <button type="button" @click="Proceed">PROCEED</button>
          </div>
      </div>

        <div class="billing_overlay" v-if="Invalid"></div>
        <div class="billing_invalid" v-if="Invalid">
            <div @click="Invalid = false">x</div>
            {{ ErrorText }}
        </div>
  </div>
</template>

<script>
export default {
    props: ['maxNumberInput',''],
    data(){
        return{
            Asterisk: true,
            toggleImg: true,
            PhoneNumber: '',
            PhoneNumberAsterisk: '',
            Invalid: false, 
            ErrorText: ''
        }
    },
    methods:{
        turnToggle: function(){
            this.Asterisk = !this.Asterisk;
            this.toggleImg = !this.toggleImg;
        },
        numKey: function(num){
            if(num == 10){
                num = 0;
            }
            const number = this.PhoneNumber+''+num;

            if(this.PhoneNumber.length < this.maxNumberInput){
                this.PhoneNumber = number;
                this.PhoneNumberAsterisk = number.replace(/./g, '*');
            }
        },
        backSpace: function(){
            this.PhoneNumber = this.PhoneNumber.slice(0,-1);
            this.PhoneNumberAsterisk = this.PhoneNumberAsterisk.slice(0,-1);
        },
        Proceed(){
            if(this.PhoneNumber.length == 0){
                this.Invalid = true;
                this.ErrorText = 'Please input telephone number';
            }else{
                this.Invalid = true;
                this.ErrorText = 'Invalid Telephone Number';
            }
        }
    }
}
</script>