<template>
  <div>
    <CHeader></CHeader>
    <CTitle
      :title="this.position"
      desc="源自碧樱家的魅力，真实展现实木家具质感"
    ></CTitle>
    <div
      class="items"
      :class="{'pink': !(i % 2)}"
      v-for="i in Math.floor(data.length / 4) + 1"
      :style="{
        'padding-top': i - 1 ? 0 : '30px',
        'padding-bottom': i - 4 ? 0 : '30px'
      }"
    >
      <a v-for="(item, index) in data.slice(i * 4 - 4, i * 4)" :href="'#/goods/' + item.fid" :key="index"><Item :data="item"></Item></a>
      <template v-if="data.slice(i * 4 - 4, i * 4).length % 4">
        <Item class="blank" v-if="data.slice(i * 4 - 4, i * 4).length % 4 <= 3"></Item>
        <Item class="blank" v-if="data.slice(i * 4 - 4, i * 4).length % 4 <= 2"></Item>
        <Item class="blank" v-if="data.slice(i * 4 - 4, i * 4).length % 4 <= 1"></Item>
      </template>
    </div>
    <CFooter></CFooter>
  </div>
</template>

<script>
  import CHeader from './CHeader'
  import CTitle from './CTitle'
  import Creed from './Creed'
  import Sale from './Sale'
  import Shop from './Shop'
  import Wechat from './Wechat'
  import Service from './Service'
  import CFooter from './CFooter'
  import Item from './Item'
  import 'whatwg-fetch'
  import { root } from '../utils'

  export default {
    name: 'products',
    components: {
      CHeader,
      CTitle,
      Creed,
      Sale,
      Shop,
      Wechat,
      Service,
      CFooter,
      Item
    },
    data () {
      return {
        order: false,
        data: [],
        position: this.$route.params.id
      }
    },
    mounted () {
      this.request()
    },
    methods: {
      request () {
        fetch(`${root}client/furnitureIndex/?category=${this.position}`, {
          method: 'GET',
          credentials: 'include'
        })
          .then((res) => {
            return res.json()
          })
          .then((data) => {
            if (!data.error) {
              this.data = data.body
            }
          })
      }
    }
  }
</script>

<style scoped>
  .items {
    padding: 0 150px;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    align-items: flex-start;
  }

  .blank {
    height: 1px;
    margin: 0 !important;
    visibility: hidden;
  }

  .pink {
    background: #fef5f5;
  }

  @media (max-width: 1366px) {
    .items {
      padding: 0 120px;
    }
  }
</style>
