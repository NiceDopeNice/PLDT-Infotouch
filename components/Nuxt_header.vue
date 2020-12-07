<template>
  <div id="nuxt_header">
        <div class="idle_timer" v-if="idleToggle">
            <v-idle @idle="onidle" :duration="300" />
        </div>
        <div class="loading" v-if="loading">
          <div class="sk-circle">
            <div class="sk-circle1 sk-child"></div>
            <div class="sk-circle2 sk-child"></div>
            <div class="sk-circle3 sk-child"></div>
            <div class="sk-circle4 sk-child"></div>
            <div class="sk-circle5 sk-child"></div>
            <div class="sk-circle6 sk-child"></div>
            <div class="sk-circle7 sk-child"></div>
            <div class="sk-circle8 sk-child"></div>
            <div class="sk-circle9 sk-child"></div>
            <div class="sk-circle10 sk-child"></div>
            <div class="sk-circle11 sk-child"></div>
            <div class="sk-circle12 sk-child"></div>
          </div>
      </div>

      <div :class="'footer '+footerColor" v-if="footer">
          <nuxt-link :to="to" v-if="backButton">
              <div class="a_img_holder">
                  <img src="img/back.svg" alt="">
              </div>
              <span>BACK</span>
          </nuxt-link>
          <nuxt-link to="/" v-if="homeButton">
              <div class="a_img_holder">
                  <img src="img/Home-Icon.png" alt="">
              </div>
              <span>HOME</span>
          </nuxt-link>

          <nuxt-link v-if="menuButton && (footerColor == 'pldt_footer')" to='/pldt'>
              <div class="a_img_holder">
                  <img src="img/menu.svg" alt="">
              </div>
              <span>MENU</span>
          </nuxt-link>

          <nuxt-link v-else-if="menuButton && (footerColor == 'smart_footer')" to='/smart'>
              <div class="a_img_holder">
                  <img src="img/menu.svg" alt="">
              </div>
              <span>MENU</span>
          </nuxt-link>

      </div>

      <transition name="fade" v-if="errorToggle">
        <div class="error_popup" v-if="mapError">
          <img src="img/error_icon.png" alt="">
            <p>
              We're having technical issues at the moment,<br />
              Please try again later.
            </p>
            <div class="error_button_holder">
              <button type="button" @click="removeError">
                OKAY
              </button>
            </div>
        </div>
        <div class="error_popup" v-else>
          <img src="img/error_icon.png" alt="">
            <p>
              We're having technical issues at the moment,<br />
              Please try again later.
            </p>
            <div class="error_button_holder">
              <nuxt-link to="/">
                GO HOME
              </nuxt-link>
            </div>
        </div>
      </transition>

  </div>
</template>


<style>
  .error_popup{
    position: absolute;
    width: 80%;
    margin: 0 auto;
    background: #ffffff;
    border-radius: 15px;
    font-size: 2.5em;
    top: 35%;
    left: 0px;
    right: 0px;
    z-index: 9999;
    padding: 15px 15px 25px 15px;
    text-align: center;
  }
  .error_button_holder{
    width: 300px;
    margin:0 auto;
  }
  .error_popup p{
    padding: 10px 0px 30px 0px;
  }
  .error_popup a, .error_popup button{
    color: #635c5c;
    padding: 10px 35px;
    text-align: center;
    font-size: 0.7em;
    border: none;
    background: #f9faf9;
    display: inline-block;
    border-radius: 2.5em;
    box-shadow: 10px 10px 10px rgba(0, 0, 0, 0.18);
    margin-top: 10px;
    margin-bottom: 10px;
    outline: none;
  }
</style>

<script>

export default {
  props: ['loading', 'idleToggle', 'footer', 'footerColor', 'homeButton', 'backButton', 'menuButton', 'errorToggle', 'mapError'],
  computed: {
      to () {
          if (this.client || !this.$routerHistory || !this.$routerHistory.hasPrevious()) {
              // probably ssr, or hasn't navigated yet.
              return { path: '/' };
          }
          return { path: this.$routerHistory.previous().path };
      }
  },
  methods: {
    onidle() {
      this.$router.push({ name: 'index'})
    },
    removeError(){
      this.errorToggle = false;
      this.loading = false;
    }
  }
}
</script>
