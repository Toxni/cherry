<template>
  <div class="shot">
    <h1 :style="{'font-size': small ? '20px' : '32px'}">
      {{title}}
    </h1>
    <template v-if="!price && !wood">
      <swiper :options="swiperOption" class="box">
        <swiper-slide v-for="(image, index) in images" class="swiper" :key="index" :style="{'width': width}">
          <img :src="image">
        </swiper-slide>
      </swiper>
      <div class="pagination"></div>
      <div class="prev arr" slot="button-prev"><img src="../../assets/left.svg"></div>
      <div class="next arr" slot="button-next"><img src="../../assets/right.svg"></div>
    </template>
    <template v-if="wood">
      <swiper :options="swiperOption" class="box">
        <swiper-slide v-for="(image, index) in images" class="swiper wood" :key="index" :style="{'width': width}">
          <div class="container">
            <img :src="image.image">
            <span>{{image.name}}</span>
          </div>
        </swiper-slide>
      </swiper>
      <div class="pagination"></div>
      <div class="prev arr" slot="button-prev"><img src="../../assets/left.svg"></div>
      <div class="next arr" slot="button-next"><img src="../../assets/right.svg"></div>
    </template>
    <div class="images" v-if="price">
      <img v-for="item in images" :src="item">
    </div>

    <p>{{desc}}</p>
    <div v-if="price" class="price">
      <h4>参考价 <span>￥{{price}}</span></h4>
      <div class="divider"></div>
      <p>{{info}}</p>
    </div>
  </div>
</template>

<script>
  import {
    swiper,
    swiperSlide
  } from 'vue-awesome-swiper'

  export default {
    name: 'shot',
    components: {
      swiper,
      swiperSlide
    },
    props: {
      images: {
        type: Array,
        default: function () {
          return []
        }
      },
      title: {
        type: String,
        default: '标题'
      },
      small: {
        type: Boolean,
        default: false
      },
      desc: {
        type: String,
        default: ''
      },
      price: {
        type: String,
        default: ''
      },
      info: {
        type: String,
        default: ''
      },
      wood: {
        type: Boolean,
        default: false
      }
    },
    data () {
      return {
        width: (window.innerWidth - 300) / 4,
        swiperOption: {
          autoplay: 2500,
          setWrapperSize: true,
          grabCursor: true,
          pagination: '.pagination',
          paginationType: 'fraction',
          prevButton: '.prev',
          nextButton: '.next',
          paginationClickable: true,
          speed: 1000,
          slidesPerView: 4,
          loop: true
        }
      }
    }
  }
</script>

<style scoped>
  .shot {
    margin: 40px 150px;
    overflow: hidden;
    position: relative;
  }

  .shot h1 {
    color: #444;
    text-align: center;
  }

  .shot p {
    color: #999;
    font-size: 16px;
    text-indent: 2em;
    margin-bottom: 20px;
  }

  .box {
    text-align: center;
  }

  .images {
    display: flex;
    justify-content: space-between;
  }

  .swiper {
    display: inline-block;
    position: relative;
  }

  .images img,
  .swiper img {
    width: 240px;
    height: 240px;
    margin: 45px auto 55px;
  }

  .wood img {
    width: 180px;
    height: 356px;
  }

  .wood .container {
    width: 180px;
    height: 356px;
    position: relative;
    margin: auto;
  }

  .wood span {
    position: absolute;
    display: block;
    width: 180px;
    height: 356px;
    background: rgba(240, 50, 50, 0.2);
    top: 0;
    left: 0;
    margin: 45px auto 55px;
    font-size: 32px;
    color: rgba(255, 255, 255, 0.8);
    box-sizing: border-box;
    padding: 60px;
    line-height: 72px;
  }

  .wood:hover span {
    display: none;
  }

  .pagination {
    position: absolute;
    left: 50%;
    bottom: 15px;
    margin-left: -8px;
    color: white;
    stroke: 1px;
    font-family: sans-serif;
    user-select: none;
  }

  .arr {
    width: 50px;
    height: 50px;
    top: 45%;
    position: absolute;
    margin: 10px;
  }

  .arr img {
    width: 50px;
    height: 50px;
  }

  .prev {
    left: -5px;
  }

  .next {
    right: -5px;
  }

  .divider {
    height: 1px;
    width: 189px;
    background: #f0f0f0;
    display: inline-block;
    margin: 10px auto 5px;
  }

  .price {
    text-align: right;
  }

  .price h4 {
    color: #636363;
    font-size: 16px;
  }

  .price h4 span {
    color: #ff6f6f;
    font-size: 16px;
  }

  .price p {
    color: #b2b2b2;
  }

  @media (max-width: 1366px) {
    .shot {
      margin: 40px 120px;
    }

    .images img {
      width: 210px;
      height: 210px;
    }
  }
</style>
