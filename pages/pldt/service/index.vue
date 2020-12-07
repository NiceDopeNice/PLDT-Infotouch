<template>
    <div class="product_page">

        <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'pldt_footer'" :homeButton="true" :backButton="true" :menuButton="true" />

        <img class="product_logo" src="img/pldt.png" alt="" />

          <transition name="fade">
            <section class="availability_container" v-if="show">
                <div class="availability_main">
                    <p>Check what service is available at your location</p>

                    <!-- KEYBOARD -->
                    <div class="input_address">
                        <input type="text" id="inAddress" autocomplete="off" @click="ChangeFocus" :placeholder="this.brand == 'pldt_key' ? 'Input address here' : 'Enter City or Municipality' " :value="inputAddress">
                        <div class="keyboard" v-if="keyboardToggle">
                            <div :class="ToUpperCase ? 'keyrow tUppercase '+this.brand : 'keyrow '+this.brand">
                            <button type="button" v-for="n in 10" @click="keyBoardFunc(n)" :key="n"><span v-if="n < 10">{{ n }}</span> <span v-else> 0</span></button>
                            <button type="button" class="backspace" @click="keyBoardFunc('del')">del</button>
                            </div>
                            <div :class="ToUpperCase ? 'keyrow tUppercase '+this.brand : 'keyrow '+this.brand">
                            <button type="button" v-for="value in QtoP" @click="keyBoardFunc(value)" :key="value">{{ value }}</button>
                            <button type="button" class="backspace" @click="keyBoardFunc('caps')">Caps</button>
                            </div>
                            <div :class="ToUpperCase ? 'keyrow tUppercase '+this.brand : 'keyrow '+this.brand">
                            <button type="button" v-for="value in AtoL" @click="keyBoardFunc(value)" :key="value">{{ value }}</button>
                            <button type="button" class="enter" @click="keyBoardFunc('enter')">Enter</button>
                            </div>
                            <div :class="ToUpperCase ? 'keyrow tUppercase last_row '+this.brand : 'keyrow last_row '+this.brand">
                            <button type="button" v-for="value in Ztoplus" @click="keyBoardFunc(value)" :key="value">{{ value }}</button>
                            </div>
                            <div :class="ToUpperCase ? 'keyrow tUppercase '+this.brand : 'keyrow '+this.brand">
                            <button type="button" class="space" @click="keyBoardFunc('space')">space</button>
                            </div>

                        </div>
                    </div>

                    <div class="search_bar">
                      <button type="button" @click="SearchAddress">Search Address</button>
                    </div>

                    <hr class="map_divider" />

                    <div style="width: 100%; height: 550px;" class="map_container">
                        <gmap-map
                            ref="map"
                            :center="AddressActive"
                            :zoom="Center"
                            style="width: 100%; height: 300px"
                        >
                            <gmap-marker
                                :position="AddressActive"
                                :clickable="true"
                                :draggable="true"
                                :mapTypeControl="true"
                                @dragend="gMapFunc($event.latLng)"
                            >
                            </gmap-marker>                                
                            
                            <gmap-info-window 
                                :options="{
                                    pixelOffset: {
                                        width: 0,
                                        height: -45
                                    }
                                }"
                                :position="infoWindowPos"
                                :opened="infoWinOpen"
                                @closeclick="infoWinOpen=false"
                            >
                            <div v-html="infoContent"></div>
                            </gmap-info-window>
                        </gmap-map>
                    </div>

                  <div class="check-availability-contaier">
                      <button type="button" @click="GetLatLng">Check Availability</button>
                  </div>
                <div class="overlay_map" v-if="Invalid"></div>
                <div class="net_invalid" v-if="Invalid">
                    <div @click="Invalid = false">x</div>
                    {{ ErrorText }}
                </div>

                </div>
            </section>
          </transition>
    </div>

</template>

<style scoped >
    .ssc_details {
        width: 95%;
        top: 45%;
    }
    .overlay_map{
        position: fixed;
        top: 0px;
        left: 0px;
        width: 100%;
        height: 100%;
        background: #000;
        opacity: 0.5;
    }
    .net_invalid{
        padding: 55px 50px 50px 50px;
    }
    .net_invalid div{
        float: right;
        margin-top: -85px;
        margin-right: -20px;
        font-size: 1.5em;
        font-weight: bold;
    }
</style>

<script>
import axios from 'axios';
import Header from "~/components/Nuxt_header";
import KeyboardComp from "~/components/VmKey";

export default {
  layout:'pldt',
  components: {
    Header,
    KeyboardComp
  },
  data(){
    return {
      brand: 'pldt_key',
      show: false,
      loading_toggle: true,
      showInfo: false,
      Center: 8,
      districts: [
          {district: 'Metro Manila', value: 'GMM NorthEast,GMM SouthWest', x: 14.560989, y: 121.057663, zoom: 8},
          {district: 'North Luzon', value: 'North Luzon', x: 17.245744, y: 121.022644, zoom: 8},
          {district: 'Central Luzon', value: 'Central Luzon', x: 16.035255, y: 121.217651, zoom: 8},
          {district: 'South Luzon', value: 'South Luzon', x: 13.282719, y: 122.162476, zoom: 8},
          {district: 'Visayas', value: 'Visayas', x: 10.692996, y: 123.931274, zoom: 8},
          {district: 'Mindanao', value: 'Mindanao', x: 7.427837, y: 124.370728, zoom: 8}
      ],
      infoWindowPos: { lat: 14.560989, lng: 121.057663 },
      infoWinOpen: false,
      Invalid: false,
      AddressActive: [],
      AddressDistrict: [],
      QtoP: ['q','w','e','r','t','y','u','i','o','p'],
      AtoL: ['a','s','d','f','g','h','j','k','l'],
      Ztoplus: ['z','x','c','v','b','n','m',',','.','-','_','+'],
      keyboardToggle: false,
      inputAddress: '',
      infoContent: '',
      ErrorText: '',
      LatLngDetails: false,
      Cache: true,
      CursorPosition: 0,
      ToUpperCase: false
    }
  },
  mounted() {
      this.loading_toggle = false;
      this.show = true;
      this.AddressActive = { lat: this.districts[0].x, lng: this.districts[0].y };
      this.AddressDistrict = this.districts[0];
  },
  methods: {
        SearchAddress(){
          this.loading_toggle = true;
          if(this.inputAddress == '' || this.inputAddress.length <= 2){
              setTimeout(() => {
                this.loading_toggle = false
                this.Invalid = true;
                this.ErrorText = 'Input your address';
                this.LatLngDetails = false;
              }, 500);
          }else{          
            const geocoder = new google.maps.Geocoder()
            const address = this.inputAddress.trim()
            this.infoWinOpen = false;
            const vm = this;
            geocoder.geocode({ address }, function (results, status) {
              if (status === 'OK') {
                const completeaddress = results[0].formatted_address;
                const latitude = results[0].geometry.location.lat();
                const longitude = results[0].geometry.location.lng();
                vm.Center = 20;
                vm.AddressActive = { lat: latitude, lng: longitude };
                vm.infoWindowPos = { lat: latitude, lng: longitude };
                vm.infoWinOpen = true;
                vm.infoContent = vm.getInfoWindowContent(completeaddress);
                vm.LatLngDetails = true;
                vm.loading_toggle = false
              }else{
                setTimeout(() => { 
                    vm.loading_toggle = false
                    vm.Invalid = true;
                    vm.ErrorText = 'Location not found';
                    vm.LatLngDetails = false;
                }, 500);
              }
            })
          }

        },
        gMapFunc(evnt) {
            this.infoWinOpen = false;
            this.infoContent = this.getInfoWindowContent('');
            this.AddressActive = { lat: evnt.lat(), lng: evnt.lng() };
        },
        GetLatLng: function(){
            this.loading_toggle = true;
            if(this.LatLngDetails){
                setTimeout(() => {
                    this.loading_toggle = false;
                    alert(this.AddressActive.lat+' - '+this.AddressActive.lng);
                }, 500);
            }else{
                setTimeout(() => {
                    this.loading_toggle = false;
                    this.Invalid = true;
                    this.ErrorText = 'Location not found. Please check your input';
                }, 500);
            }
        },
        getInfoWindowContent: function (completeaddress) {
            return (`<div class="ssc_details">
            <div class="ssc_address">${completeaddress}</div>
            </div>`);
        },
        ChangeFocus: function(){
            const input = document.getElementById('inAddress');
            this.CursorPosition = this.inputAddress.slice(0, input.selectionStart).length - 1;
            this.Cache = false;
            this.keyboardToggle = true;
            this.Invalid = false;
        },
        keyBoardFunc: function(val){
            const input = document.getElementById('inAddress');
            const inputval = this.inputAddress;
            const CursorStart = this.inputAddress.slice(0, input.selectionStart).length;
            const textLength = this.inputAddress.length;

            if(this.Cache){
                input.focus();
            }

            if(val == "del"){
                if(CursorStart == textLength){
                this.inputAddress = inputval.slice(0, -1);
                input.focus();
                // alert(1)
                }else{
                const textBefore = this.inputAddress.substring(0,  this.CursorPosition );
                const textAfter  = this.inputAddress.substring( this.CursorPosition + 1, textLength  );

                // Output
                this.inputAddress = textBefore+''+textAfter
                // alert(2)
                }
            }else if(val == "caps"){
                if(CursorStart == textLength){
                input.focus();
                }
                // Output
                this.ToUpperCase = !this.ToUpperCase;
            }else if(val == "space"){
                // Output
                this.inputAddress += ' ';
                // input.focus();
            }else if(val == "enter"){
                this.keyboardToggle = false;
            }else{
                if(CursorStart == textLength){
                input.focus();
                }
                if(this.ToUpperCase){
                // Output
                this.inputAddress += val.toUpperCase();
                }else{
                // Only in first letter
                if(this.Cache){
                    this.CursorPosition = CursorStart;
                }else{
                    this.CursorPosition = this.inputAddress.slice(0, this.CursorPosition).length + 1;
                }

                const textBefore = this.inputAddress.substring(0,  this.CursorPosition );
                const textAfter  = this.inputAddress.substring( this.CursorPosition, textLength );
                // Output
                this.inputAddress = textBefore+''+val+''+textAfter;
                // alert(textBefore+''+val+''+textAfter);

                }
            }

        }
  }
}


</script>