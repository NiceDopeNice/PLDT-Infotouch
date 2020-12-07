<template>
    <div class="product_page">

        <Header :loading="loading_toggle" :idleToggle="true" :footer="true" :footerColor="'pldt_footer'" :homeButton="true" :backButton="true" :menuButton="true" />

        <img class="product_logo" src="img/pldt.png" alt="" />

          <transition name="fade">
            <section class="service_repair_container" v-if="show">

                <div class="service_repair_menu">
                  <nuxt-link to="new/check-availability">
                    <img src="img/house.png" alt="">
                    <p>HOUSE</p>
                  </nuxt-link>
                </div>
                <div class="service_repair_menu">
                  <nuxt-link to="new/check-availability">
                    <img src="img/condominium.png" alt="">
                    <p>CONDOMINIUM</p>
                  </nuxt-link>
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
      loading_toggle: true
    }
  },
  mounted() {
        this.loading_toggle = false;
        this.show = true;
  }
}


</script>