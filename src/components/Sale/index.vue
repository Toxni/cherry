<template>
  <div class="sale">
    <h1>优品热卖</h1>
    <div class="box" :style="{'margin-left': width > 1280 ? margin - 60 : 147 + 'px'}">
      <swiper :options="swiperOption">
        <swiper-slide class="swiper" v-for="(item, index) in sale" :key="index">
          <img :src="item">
        </swiper-slide>
      </swiper>
    </div>
    <div class="arr prev" :style="{'left': margin - 60 + 'px'}" slot="button-prev"><img src="../../assets/left.svg"></div>
    <div class="arr next" :style="{'right': margin - 60 + 'px'}"  slot="button-next"><img src="../../assets/right.svg"></div>
    <a href="#/products/橱柜"><p class="more"><CButton text="更多优品"></CButton></p></a>
  </div>
</template>

<script>
  import {
    swiper,
    swiperSlide
  } from 'vue-awesome-swiper'

  import CButton from '../CButton'
  import { root } from '../../utils'
  import 'whatwg-fetch'

  export default {
    name: 'sale',

    components: {
      CButton,
      swiper,
      swiperSlide
    },

    mounted () {
      this.request()
    },
    methods: {
      request () {
        fetch(`${root}client/homeIndex/`, {
          method: 'GET',
          credentials: 'include'
        })
          .then((res) => {
            return res.json()
          })
          .then((data) => {
            if (!data.error) {
              this.sale = data.body.hotsale
            }
          })
      }
    },

    data () {
      return {
        width: window.innerWidth,
        margin: window.innerWidth <= 1366 ? parseInt((window.innerWidth - 1140) / 8) + 190 : parseInt((window.innerWidth - 1260) / 8) + 210,
        swiperOption: {
          autoplay: 2500,
          setWrapperSize: true,
          grabCursor: true,
          prevButton: '.prev',
          nextButton: '.next',
          spaceBetween: 60,
          width: window.innerWidth > 1280 ? window.innerWidth / 3 * 2 : 853,
          loop: true,
          loopAdditionalSlides: 5,
          slidesPerView: 2,
          speed: 1000
        },
        sale: []
      }
    }
  }
</script>

<style scoped>
  .sale {
    overflow: hidden;
    position: relative;
    height: 600px;
    background: #fef5f5;
  }

  .swiper {
    text-align: center;
    display: inline-block;
  }

  .sale h1 {
    color: #444;
    font-size: 32px;
    margin: 60px auto 76px;
    text-align: center;
  }

  .swiper img {
    width: 420px;
    height: 240px;
    margin-right: -60px;
  }

  .arr {
    width: 50px;
    height: 50px;
    top: 45%;
    position: absolute;
    cursor: pointer;
    margin: 10px;
  }

  .arr img {
    width: 50px;
    height: 50px;
  }

  .more {
    margin: 70px;
    text-align: center;
  }

  @media (max-width: 1366px) {
    .swiper img {
      width: 380px;
      height: 210px;
    }

    .arr {
      top: 43%;
    }
  }
</style>
