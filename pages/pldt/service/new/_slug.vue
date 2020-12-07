<template>
    <div class="product_page">

        <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'pldt_footer'" :homeButton="true" :backButton="true" :menuButton="true" />

        <img class="product_logo" src="img/pldt.png" alt="" />
        <div style="width: 500px; height: 500px;">
          <gmap-map
              :center="center"
              :zoom="7"
              style="width: 500px; height: 300px"
          >
              <gmap-marker
                  :key="index"
                  v-for="(m, index) in markers"
                  :position="m.position"
                  :clickable="true"
                  :draggable="true"
                  @click="center=m.position"
              ></gmap-marker>
          </gmap-map>
        </div>

          <transition name="fade">
            <section class="availability_container" v-if="show">
                <div class="availability_main">
                    <p>Check what service is available at your location</p>

                    <!-- KEYBOARD -->
                    <KeyboardComp :brand="'pldt_key'" />

                    <div class="search_bar">
                      <a href="javascript:;">Search Address</a>
                    </div>

                  <div class="check-availability-contaier">
                      <button type="button">Check Availability</button>
                  </div>
                </div>
            </section>
          </transition>
    </div>

</template>

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
      show: false,
      loading_toggle: true,
      showInfo: false,
      center: { lat: 10.0, lng: 10.0 },
      markers: [
        {
          position: { lat: 10.0, lng: 10.0 }
        },
        {
          position: { lat: 11.0, lng: 11.0 }
        }
      ]
    }
  },
  mounted() {
        this.loading_toggle = false;
        this.show = true;
  },
  methods: {
  }
}


</script>