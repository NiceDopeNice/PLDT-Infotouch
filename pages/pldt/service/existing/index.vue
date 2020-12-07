<template>
    <div class="product_page">
        <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'pldt_footer'" :homeButton="true" :backButton="true" :menuButton="true" />

        <img class="product_logo" src="img/pldt.png" alt="" />

          <transition name="fade">
            <section class="availability_container" v-if="show">
                <div class="availability_main">
                    <p>Check what service is available at your location</p>

                    <div class="existing_form">
                        <p>EXISTING CUSTOMER</p>
                        <div class="existing_input">
                            <div class="existing_areacode">
                                <label>Area Code:</label>
                                <div><input type="text"></div>
                            </div>
                            <div class="existing_telephone">
                                <label>Telephone Number:</label>
                                <div><input type="text"></div>
                            </div>
                        </div>
                        <div class="show_number">
                            <input type="checkbox" name="" id="showCheck"> <label for="showCheck">Show Number</label>
                        </div>
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

export default {
  layout:'pldt',
  components: {
    Header
  },
  data(){
    return {
      show: false,
      loading_toggle: true,
      showInfo: false
    }
  },
  mounted() {
        this.loading_toggle = false;
        this.show = true;
  }
}


</script>