<template>
  <div
    class="order"
    :style="{'display': !show ? 'none' : 'block'}"
  >
    <div class="type" v-if="!form">
      <a href="#/products/橱柜" @click="refresh('#/products/橱柜')">
        <img src="./assets/images/order-a.svg">
        <span>橱柜</span>
      </a>
      <a href="#/products/衣柜" @click="refresh('#/products/衣柜')">
        <img src="./assets/images/order-b.svg">
        <span>衣柜</span>
      </a>
      <a href="#/products/原木门" @click="refresh('#/products/原木门')">
        <img src="./assets/images/order-c.svg">
        <span>原木门</span>
      </a>
      <a href="#/products/楼梯" @click="refresh('#/products/楼梯')">
        <img src="./assets/images/order-d.svg">
        <span>楼梯</span>
      </a>
      <a href="#/products/家饰" @click="refresh('#/products/家饰')">
        <img src="./assets/images/order-e.svg">
        <span>家饰</span>
      </a>
    </div>
    <div class="form" v-else="form">
      <span>姓名</span>
      <input v-model="name">
      <span>电话</span>
      <input v-model="tel" type="number">
      <a @click="submit"><CButton text="预订" :small="true"></CButton></a>
    </div>
  </div>
</template>

<script>
  import CButton from '../CButton'
  import { root } from '../../utils'
  import 'whatwg-fetch'
  export default {
    name: 'order',

    components: {
      CButton
    },

    data () {
      return {
        name: '',
        tel: ''
      }
    },

    methods: {
      submit () {
        if ((/^1[34578]\d{9}$/.test(this.tel)) && this.name) {
          let formData = new FormData()
          formData.append('name', this.name)
          formData.append('telephone', this.tel)
          fetch(`${root}client/homeOrder/`, {
            method: 'POST',
            credentials: 'include',
            body: formData
          })
            .then((res) => {
              return res.json()
            })
            .then((data) => {
              if (!data.error) {
                alert('预定成功 我们的客服会尽快联系您')
                this.name = ''
                this.tel = ''
                window.location.reload()
              }
            })
        } else {
          alert('请输入正确的电话和姓名')
        }
      },
      refresh (href) {
        window.location.hash = href
        window.location.reload()
      }
    },

    props: {
      form: {
        type: Boolean,
        default: false
      },

      show: {
        type: Boolean,
        default: false
      }
    }
  }
</script>

<style scoped>
  .order {
    z-index: 2;
    width: 100%;
    padding: 20px 150px;
    background: rgba(255, 255, 255, 0.9);
    box-sizing: border-box;
    border-top: 1px solid #f6f6f6;
    transition: marginTop 1s ease 0s;
  }

  .type {
    display: flex;
    justify-content: space-between;
  }

  .type a {
    width: 160px;
    display: inline-block;
    line-height: 1;
  }

  .type img {
    width: 40px;
    height: 40px;
    vertical-align: middle;
  }

  .type span {
    color: #636363;
    font-size: 16px;
    margin-left: 50px;
    vertical-align: middle;
  }

  .form {
    text-align: right;
    color: #636363;
  }

  .form span {
    margin: auto 20px;
  }

  .form input {
    height: 28px;
    width: 160px;
    font-size: 16px;
    color: #636363;
    text-align: center;
    outline: none;
  }
</style>
