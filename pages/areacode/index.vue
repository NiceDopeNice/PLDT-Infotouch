<template>
    <div class="product_page">

        <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'pldt_footer'" :homeButton="true" :backButton="true" :menuButton="true" :errorToggle="errorToggle" />

        <img class="product_logo" src="img/pldt.png" alt="" />

          <transition name="fade">
            <section v-if="show">
                <div class="areacode_container" :style="zipCodeQuery == 1 ? 'margin-top:10%' : '' ">
                    <!-- Area Code -->
                    <div v-if="zipCodeQuery != 1">

                        <p class="title">PHILIPPINE AREA CODES</p>

                        <label>PROVINCE:</label>
                        <select @change="selectProvince($event)">
                            <option value="">---SELECT---</option>
                            <option v-for="list in Province" :key="list.phareacodes_id" :value="list.phareacodes_province.trim()">
                                {{ list.phareacodes_province.trim() }}
                            </option>
                        </select>

                        <label>MUNICIPALITY/CITY:</label>

                        <select @change="selectCity">
                            <option value="">---SELECT---</option>
                            <option v-for="list in City" :key="list.phareacodes_id" :value="list.phareacodes_destination.trim()" :data-code="list.phareacodes_code">
                                {{ list.phareacodes_destination.trim() }}
                            </option>
                        </select>

                        <div class="area_code_details" v-show="withCode">
                            The area code for <b>{{ CityName }}</b> is: <b>{{ CityCode }}</b>
                        </div>
                    </div>

                    <!-- Zip Code -->
                    <div class="zipcode_container" v-else>

                        <p class="title">PHILIPPINE ZIP CODES</p>

                        <label>PROVINCE:</label>
                        <select @change="selectProvinceZip($event)">
                            <option value="">---SELECT---</option>
                            <option v-for="list in Province" :key="list.phareacodes_id" :value="list.phareacodes_province.trim()">
                                {{ list.phareacodes_province.trim() }}
                            </option>
                        </select>

                        <label>CITY:</label>

                        <select @change="selectCity">
                            <option value="">---SELECT---</option>
                            <option v-for="list in City" :key="list.phzipcodes_id" :value="list.phzipcodes_location.trim()" :data-code="list.phzipcodes_code">
                                {{ list.phzipcodes_location.trim() }}
                            </option>
                        </select>

                        <div class="area_code_details" v-show="withCode" style="margin-bottom:30px;">
                            The zip code for <b>{{ CityName }}</b> is: <b>{{ CityCode }}</b>
                        </div>

                        <label>NATIONAL CAPITAL REGION:</label>
                        <select @change="SelectCapitalRegion">
                            <option value="">---SELECT---</option>
                            <option v-for="list in Ncr" :key="list.phzipcodes_id" :value="list.phzipcodes_city">
                                {{ list.phzipcodes_city }}
                            </option>
                        </select>

                        <select @change="selectLocation">
                            <option value="">---SELECT---</option>
                            <option v-for="list in Location" :key="list.phzipcodes_id" :value="list.phzipcodes_location" :data-code="list.phzipcodes_code">
                                {{ list.phzipcodes_location }}
                            </option>
                        </select>

                        <div class="area_code_details" v-show="withLocationCode">
                            The zip code for <b>{{ LocationName }}</b> is: <b>{{ LocationCode }}</b>
                        </div>
                    </div>

                </div>
            </section>
          </transition>
    </div>
</template>

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
      Province: [],
      City: [],
      CityName: '',
      CityCode: '',
      Ncr: [],
      Location: [],
      LocationName: '',
      LocationCode: '',
      show: false,
      withCode: false,
      withLocationCode: false,
      loading_toggle: true,
      errorToggle: false,
      zipCodeQuery: this.$route.query.zipcode
    }
  },
  mounted() {
      this.$axios.$get('getAreaCode').then( res => {
        if(res){
          this.show = true;
          this.loading_toggle = false;
          this.Province = res;
        }
      }).catch( err => {
          console.log(err);
          this.errorToggle = true;
      });

      this.$axios.$get('getPldtZipCodeGroup').then( res => {
        if(res){
          this.show = true;
          this.loading_toggle = false;
          this.Ncr = res;
        }
      }).catch( err => {
          console.log(err);
          this.errorToggle = true;
      });
  },
    methods: {
        selectProvince(event) {
            this.loading_toggle = true;
            this.City = [];
            this.$axios.$get('getCityByProvince?province='+event.target.value).then( res => {
                if(res){
                this.loading_toggle = false;
                this.withCode = false;
                this.City = res;
                }
            }).catch( err => {
                console.log(err);
                this.errorToggle = true;
            });
        },
        selectCity: function(){
            const e = event.target;
            if(e.value == ''){
                this.withCode = false;
            }else{
                const theTarget = e.options[e.options.selectedIndex].dataset;
                this.CityName = e.value;
                this.CityCode = theTarget.code;
                this.withCode = true;
            }
        },
        selectProvinceZip(event) {
            this.loading_toggle = true;
            this.City = [];
            this.$axios.$get('getZipCityByProvince?phzipcodes_province='+event.target.value).then( res => {
                if(res){
                this.loading_toggle = false;
                this.withCode = false;
                this.City = res;
                }
            }).catch( err => {
                console.log(err);
                this.errorToggle = true;
            });
        }, 
        SelectCapitalRegion: function(){
            this.loading_toggle = true;
            if(event.target.value != ''){
                this.$axios.$get('getPldtLocationByCity?phzipcodes_city='+event.target.value).then( res => {
                    if(res){
                    this.loading_toggle = false;
                    this.withLocationCode = false; 
                    this.Location = res;
                    }
                }).catch( err => {
                    console.log(err);
                    this.errorToggle = true;
                });
            }else{
                this.Location = [];
                this.loading_toggle = false;
                this.withLocationCode = false; 
            }
        },
        selectLocation: function(){
            const e = event.target;
            if(e.value == ''){
                this.withLocationCode = false;
            }else{
                const theTarget = e.options[e.options.selectedIndex].dataset;
                this.LocationName = e.value;
                this.LocationCode = theTarget.code;
                this.withLocationCode = true;
            }
        }
    }
}


</script>