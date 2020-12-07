<template>
  <div class="product_page">

    <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'smart_footer'" :menuButton="true" :homeButton="true"  :backButton="true" :errorToggle="errorToggle" />
    <img class="product_logo" src="img/smart.png" alt="" />

    <transition name="fade">
      <div class="g_container net_finder" v-if="show">
        <p class="title">NETWORK FINDER</p>
        <p class="instuction">Check the network coverage in your location.</p>

        <KeyboardComp :brand="'smart_key'" />

        <p class="available_sub">Available to Smart, TNT, and Sun subscribers.</p>
        <button type="button" class="check_net" @click="CheckNetwork">Check my network</button>
        <hr />

        <div v-if="searchStatus">
            <div class="net_output overflow" v-if="hasResult">
                <table>
                    <thead>
                        <tr>
                            <td style="width: 230px;">PROVINCE</td>
                            <td style="width: 350px;">TOWN OR CITY</td>
                            <td style="width: 200px;">SIGNAL</td>
                        </tr>
                        <tr>
                            <td></td>
                            <td></td>
                            <td>
                                <span>2G</span>
                                <span>3G</span> 
                                <span>4G </span>
                                <span class="last_g">4.5G</span>
                            </td>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(list, index) in filteredLocations" :key="index">
                            <td>{{ list.Province }}</td>
                            <td>{{ list.City }}</td>
                            <td>
                                <span><i :class="list.Bands[0] == '2G' ? 'active':''"></i></span>
                                <span><i :class="list.Bands[1] == '3G' ? 'active':''"></i></span>
                                <span><i :class="list.Bands[2] == '4G' ? 'active':''"></i></span>
                                <span><i :class="list.Bands[3] == '4.5G' ? 'active':''"></i></span>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>

            <div class="net_output" v-else>
                <h1>No results found.</h1>
                <img src="img/nosignal.png" alt="">
            </div>

        </div>

        <div class="net_invalid" v-if="invalid">
            Invalid location. Please check your input.
        </div>

      </div>
    </transition>
 
  </div>
</template>

<script>
import axios from 'axios';
import Header from "~/components/Nuxt_header";
import KeyboardComp from "~/components/VmKey";

export default {
  layout:'smart',
  components: {
    Header,
    KeyboardComp
  },
  data(){
    return {
      searchStatus: false,
      show: false,
      hasResult: false,
      loading_toggle: true,
      errorToggle: false,
      invalid: false,
      getAllLocation: [],
      filteredLocations: [],
      inputAddress: ''
    }
  },
  mounted() {
      axios.get('network_finder.json').then( res => {
        if(res){
          this.show = true;
          this.loading_toggle = false;
          this.getAllLocation = res.data.result;
        }
      }).catch( err => {
          console.log(err);
          this.errorToggle = true;
      });
  },
  methods: {
    CheckNetwork: function(){
        const inputValue = document.getElementById("inAddress").value;
        this.loading_toggle = true;
        if(inputValue.length > 1){

          setTimeout(() => {
            this.filteredLocations = [];
            this.getAllLocation.forEach(
                
                location => {
                    if (
                        location.Province.toLowerCase().indexOf(inputValue.toLowerCase()) != -1 ||
                        location.City.toLowerCase().indexOf(inputValue.toLowerCase()) != -1
                    ) {
                        this.filteredLocations.push(Object.assign({}, location));
                    }
                }
            );

            if(this.filteredLocations.length > 0){
              this.hasResult = true;
            }else{
              this.hasResult = false;
            }
            this.searchStatus = true;
            this.loading_toggle = false;
          }, 1000);

        }else{

          setTimeout(() => {
            this.searchStatus = false;
            this.invalid = true;
            setTimeout(() => {
              this.invalid = false;
            }, 2000);
            this.loading_toggle = false;
          }, 300);

        }

    }
  }
}


</script>