<template>
    <div class="product_page">

        <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'pldt_footer'" :homeButton="true" :backButton="true" :menuButton="true" 
        :errorToggle="errorToggle" :mapError="mapError" />

        <img class="product_logo" src="img/pldt.png" alt="" />

          <transition name="fade">
            <div class="sscs_holder" v-if="show">
                <div class="sscs_district">
                    <VueSlickCarousel v-bind="slickOptions">
                        <button @click="ChangeDistrict(SSCSDistrict[count], index)" class="district_button" :class="{ 'active': index === 0 }" type="button" v-for="(count, index) in DistrictArray" :key="index">
                            {{ SSCSDistrict[count] == 'NCR' ? 'Metro Manila' : SSCSDistrict[count] }}
                        </button>
                    </VueSlickCarousel>
                </div>
                
                <section class="sscs_container">
                    <div class="sscs_main">
                        <h1>STORE LOCATOR</h1>
                        <div class="scc_list_main" v-if="sscs_list">
                            <div class="ssc_list" :class="'tggl'+index" @click="ChangeSscActive(sscList,index)" v-for="(sscList, index) in SSCSListPerDistrict" :key="index">
                                <p><b>{{ sscList.ssc_name }}</b></p>
                                <p>Address: {{ sscList.ssc_address }}</p>
                                <p>Business Hours: {{ sscList.ssc_bh }}</p>
                            </div>
                        </div>
                        <div style="width: 100%; height: 550px;" class="map_container" v-if="SSCSListPerDistrict">
                            <gmap-map
                                ref="map"
                                :center="{ lat: this.districtActive.x, lng: this.districtActive.y }"
                                :zoom="8"
                                style="width: 100%; height: 300px"
                            >
                                <gmap-marker
                                    :key="index"
                                    v-for="(m, index) in markers"
                                    :position="{ lat: m.ssc_lat, lng: m.ssc_lng }"
                                    :clickable="true"
                                    :draggable="false"
                                    :mapTypeControl="true"
                                    :icon="'img/marker_icon.png'"
                                    @click="toggleInfoWindow(m,index)">
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
                        <button type="button" v-if="sscs_list" class="ssc_get_direc" :class="ActiveSsc ? '':'disable'" @click="GetDirection">Get Directions</button>
                        <div id="direction-panel" v-if="sscs_list == false"></div>
                    </div> 
                </section>
            </div>
          </transition>
    </div>

</template>

<style>
/* *{
    overflow: inherit !important;
} */
</style>

<script>
import axios from 'axios';
import Header from "~/components/Nuxt_header";
import $ from 'jquery'
import 'vue-slick-carousel/dist/vue-slick-carousel.css'


export default {
  layout:'pldt',
  components: {
    Header
  },
  data(){
    return {
        show: false,
        loading_toggle: true,
        infoContent: '',
        errorToggle: false,
        mapError: false,
        sscs_list: true,
        ActiveSsc: '',
        getSSCSList: [],
        SSCSListPerDistrict: [],
        SSCS: [],
        DistrictArray: [2, 4, 1, 3, 0, 5],
        infoWindowPos: { lat: 14.560989, lng: 121.057663 },
        SSCSDistrict: [],
        districtActive: [],
        markers: [],
        districts: [
            {district: 'Metro Manila', value: 'GMM NorthEast,GMM SouthWest', x: 14.560989, y:121.057663, zoom: 8},
            {district: 'North Luzon', value: 'North Luzon', x: 17.245744, y: 121.022644, zoom: 8},
            {district: 'Central Luzon', value: 'Central Luzon', x: 16.035255, y: 121.217651, zoom: 8},
            {district: 'South Luzon', value: 'South Luzon', x: 13.282719, y: 122.162476, zoom: 8},
            {district: 'Visayas', value: 'Visayas', x: 10.692996, y: 123.931274, zoom: 8},
            {district: 'Mindanao', value: 'Mindanao', x: 7.427837, y: 124.370728, zoom: 8}
        ],
        slickOptions: {
            infinite: false,
            dots: false,
            slidesToShow: 3,
            slidesToScroll: 3
        },
        infoWinOpen: false,
        currentMidx: null
    }
  },
  mounted() {

      this.districtActive = this.districts[0];
      this.$axios.$get('getAllSSCS?ssc_group=pldt').then( res => {
        if(res){
            this.show = true;
            this.loading_toggle = false;
            this.getSSCSList = res;
            this.getSSCSList.forEach((element, index) => {
                if(element.ssc_district  == 'NCR'){
                    this.markers.push(element);
                    this.SSCSListPerDistrict.push(element);
                }
            });
            
          this.getSSCSList.forEach((element, index) => {
            this.SSCSDistrict.push(element.ssc_district);
          });

          this.SSCSDistrict = [ ...new Set(this.SSCSDistrict) ];
        }
      }).catch( err => {
          console.log(err);
          this.errorToggle = true;
      });
  },
    methods: {
        toggleInfoWindow: function (marker, idx) {
            $('.ssc_list').removeClass("active");
            $('.tggl'+idx).addClass("active");

            this.ActiveSsc = marker.ssc_lat+','+marker.ssc_lng;

            this.infoWindowPos = { lat: marker.ssc_lat, lng: marker.ssc_lng };
            this.infoContent = this.getInfoWindowContent(marker);
            this.infoWinOpen = false;
            setTimeout(() => {
                this.infoWinOpen = true;
            }, 100);
        },
        getInfoWindowContent: function (marker) {
            return (`<div class="ssc_details">
            <div class="ssc_name">${marker.ssc_name}</div>
            <div class="ssc_sched">${marker.ssc_bh}</div>
            <div class="ssc_address">${marker.ssc_address}</div>
            </div>`);
        },
        ChangeDistrict: function(district_name, count){
            const thisEvent = $(event.target);
            $('.sscs_district .district_button').removeClass("active");
            $('.ssc_list').removeClass("active");
            thisEvent.addClass('active');
            this.sscs_list = true;
            this.ActiveSsc = "";
            $("#direction-panel").html("");

            this.markers = [];
            this.SSCSListPerDistrict = [];
            this.infoWinOpen = false;
            this.loading_toggle = true;

            setTimeout(() => {            
                this.getSSCSList.forEach((element, index) => {
                    if(element.ssc_district  == district_name){
                        this.markers.push(element);
                        this.SSCSListPerDistrict.push(element);
                    }
                });
                this.districtActive = this.districts[count];
                this.loading_toggle = false;
            }, 500);
        },
        ChangeSscActive: function (marker, idx) {
            $('.ssc_list').removeClass("active");
            $('.tggl'+idx).addClass("active");

            this.ActiveSsc = marker.ssc_lat+','+marker.ssc_lng;

            this.infoWindowPos = { lat: marker.ssc_lat, lng: marker.ssc_lng };
            this.infoContent = this.getInfoWindowContent(marker);
            this.infoWinOpen = false;
            setTimeout(() => {
                this.infoWinOpen = true;
            }, 100);
        },
        GetDirection: function(marker){
            this.loading_toggle = true;

            if(this.ActiveSsc != ""){
                this.directionsService = new google.maps.DirectionsService()
                this.directionsDisplay = new google.maps.DirectionsRenderer()
                this.directionsDisplay.setMap(this.$refs.map.$mapObject)
                
                const vm = this
                vm.directionsService.route({
                    origin: '14.5590575,121.0178861', // Can be coord or also a search query
                    destination: this.ActiveSsc,
                    travelMode: 'DRIVING'
                }, function (response, status) {
                    if (status === 'OK') { 
                        vm.loading_toggle = false;
                        vm.sscs_list = false;
                        vm.markers = [];
                        vm.SSCSListPerDistrict = [];
                        vm.infoWinOpen = false;

                        setTimeout(() => {
                            vm.directionsDisplay.setPanel(document.getElementById('direction-panel'));
                            vm.directionsDisplay.setDirections(response) // draws the polygon to the map
                        }, 300);
                        
                    } else {
                        vm.errorToggle = true;
                        vm.mapError = true;
                        console.log('Directions request failed due to ' + status)
                    }
                })
            }else{
                this.loading_toggle = false;
            }
        }
    }
}

</script>