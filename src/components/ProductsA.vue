<template>
  <div>
    <AHeader></AHeader>
    <h1>家具详情
      <select v-model="position" @change="changePosition">
        <option>橱柜</option>
        <option>衣柜</option>
        <option>原木门</option>
        <option>楼梯</option>
        <option>家饰</option>
      </select>
    </h1>
    <div class="cards">
      <div class="card" v-for="(item, index) in total">
        <div class="select">
          <div
            @click="showDetail(index)"
            class="banner"
            style="margin: 0"
          >
            <img :src="image[index]">
          </div>
        </div>
        <div>
          <input placeholder="标题@label label label" v-model="name[index]">

          <textarea v-model="intro[index]">
            这里填充简介，上面填充标题和 label。标题与 label 用 @ 分割，label 与 label 之间用空格分割，形如『标题@label babel』即可。
          </textarea>
          <span>价格： </span><input class="priceInput" placeholder="" v-model="price[index]">
        </div>
        <p class="button">
          <a @click="showDetail(index)"><CButton text="编辑" color="#666" :small="true"></CButton></a>
          <a @click="delItem(index)"><CButton text="删除" color="hotpink" :small="true"></CButton></a>
        </p>
      </div>
    </div>
    <p class="button" @click="">
      <a @click="appendItem"><CButton color="pink" text="新增项目"></CButton></a>
    </p>

    <!--detail-->

    <div class="detail" v-if="detail">
      <span class="close" @click="hideDetail">x</span>
      <h1>{{name[now]}}</h1>
      <div>
        <p>标题: </p><input placeholder="" v-model="name[now]">

        <p>标题简述: </p>
        <textarea v-model="intro[now]">
          这里填充简介，上面填充标题和 label。标题与 label 用 @ 分割，label 与 label 之间用空格分割，形如『标题@label babel』即可。
        </textarea>
        <p>价格: </p><input placeholder="" v-model="price[now]">
        <p>尺寸: </p><input placeholder="" v-model="size">
        <p>材质:</p>
        <template v-for="(item, index) in wood">
          <span>{{item.name}}：</span>
          <input class="checkbox" type="checkbox" v-model="material[index]" :checked="true">
        </template>
      </div>

      <p>题图: </p>
      <div class="select" @click="onOperate">
        <span class="upload" v-if="(uploadBtn.indexOf('main' + now + '@') + 1) && operate" @click="upload('main' + now)">上传</span>
        <span class="success" v-if="uploadSuc.indexOf('main' + now + '@') + 1">成功</span>
        <picture-input
          :ref="'main' + now"
          @change="onChange('main' + now)"
          @remove="onRemove"
          width="180"
          height="180"
          margin="0"
          class="banner"
          style="margin: 0"
          accept="image/*"
          size="10"
          buttonClass="btn changeBtn"
          removeButtonClass="btn"
          :prefill="image[now]"
          :plain="true"
          :removable="true"
          :customStrings="{
            change: '更换',
            remove: '移除'
          }"
        >
        </picture-input>
      </div>

      <p>产品参数: </p>
      <Params :isAdmin="true" :data="params" @paramsSave="paramHandle"></Params>

      <p> 详细信息: </p>
      <vue-editor
        class="editor"
        ref="myTextEditor"
        v-model="content"
        @imageAdded="handleImageAdded"
        :useCustomImageHandler="true"
      >
      </vue-editor>

      <p>维护保养: </p>
      <Service :admin="true" :fix="true" :data="maintain" @maintainSave="maintainHandle" class="service"></Service>

      <p class="submit" @click="save"><CButton color="#333" text="保存"></CButton></p>
    </div>
    <div class="mask" v-if="detail" @click="hideDetail"></div>
  </div>
</template>

<script>
  import AHeader from './AHeader'
  import Banner from './Banner'
  import Creed from './Creed'
  import Sale from './Sale'
  import Shop from './Shop'
  import Wechat from './Wechat'
  import Service from './Service'
  import CButton from './CButton'
  import Params from './Params'
  import PictureInput from 'vue-picture-input'
  import 'whatwg-fetch'
  import { root } from '../utils'
  import VueEditor from './Editor/VueEditor.vue'

  export default {
    name: 'cases-admin',
    data () {
      return {
        position: window.sessionStorage.getItem('position') || '橱柜',
        now: 0,
        total: 0,
        insNum: 2,
        uploadBtn: '',
        uploadSuc: '',
        detail: false,
        name: [],
        title: [],
        labels: [],
        intro: [],
        image: [],
        price: [],
        fid: -1,
        data: [],
        content: '',
        size: '',
        material: [],
        params: {},
        maintain: {},
        serial: [],
        wood: [],
        allWood: '',
        operate: false
      }
    },
    components: {
      AHeader,
      Banner,
      Creed,
      Sale,
      Shop,
      Wechat,
      Service,
      Params,
      CButton,
      PictureInput,
      VueEditor
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
              this.total = this.data.length
              for (let i = 0; i < this.data.length; i++) {
                this.image[i] = this.data[i].image
                this.intro[i] = this.data[i].intro
                this.price[i] = this.data[i].price
                this.name[i] = this.data[i].name
                this.serial[i] = this.data[i].serial
              }
            }
          })

        fetch(`${root}backend/material/get/`, {
          method: 'GET',
          credentials: 'include'
        })
          .then((res) => {
            return res.json()
          })
          .then((data) => {
            if (!data.error) {
              this.wood = data.body.info
            }
          })
      },
      paramHandle (e) {
        this.params = e
      },
      maintainHandle (e) {
        this.maintain = e
      },
      onOperate () {
        this.operate = true
      },
      onChange (e) {
        if (this.$refs[e].image || this.$refs[e][0].image) {
          this.uploadBtn = this.uploadBtn + e + '@'
        } else {
          alert('您的浏览器不支持 FileReader API')
        }
      },
      onRemove () {
        this.operate = false
      },
      upload (e) {
        let formData = new FormData()
        const image = this.$refs[e].file || this.$refs[e][0].file
        formData.append('image', image)
        fetch(`${root}backend/furniture/upload/`, {
          method: 'POST',
          credentials: 'include',
          body: formData
        })
          .then((res) => {
            return res.json()
          })
          .then((data) => {
            if (!data.error) {
              let num = e.match(/[0-9]+/) || 0
              if (e.indexOf('main') !== -1) {
                this.image[num] = data.body.url
              }
              this.uploadBtn.replace(e, '')
              this.uploadSuc = this.uploadSuc + e + '@'
            }
          })
      },
      hideDetail () {
        this.detail = false
      },
      showDetail (i) {
        this.now = i
        this.fid = this.data[i].fid
        fetch(`${root}client/furnitureDetail/?fid=${this.fid}`, {
          method: 'GET',
          credentials: 'include'
        })
          .then((res) => {
            return res.json()
          })
          .then((data) => {
            if (!data.error) {
              this.size = data.body.base.size
              for (let i = 0; i < this.wood.length; i++) {
                this.material[i] = JSON.stringify(data.body.base.material).indexOf(this.wood[i].name) + 1
              }
              this.params = data.body.params
              this.content = data.body.detail
              this.maintain = data.body.maintain

              this.detail = true
            }
          })
      },
      changePosition () {
        window.sessionStorage.setItem('position', this.position)
        window.location.reload()
      },
      appendItem () {
        this.now = this.total + 1
        this.fid = -1
        this.detail = true
      },
      delItem (e) {
        if (confirm('确定删除该项吗？')) {
          let formData = new FormData()
          const fid = this.data[e].fid
          formData.append('fid', fid)
          fetch(`${root}backend/furniture/delete/`, {
            method: 'POST',
            credentials: 'include',
            body: formData
          })
            .then((res) => {
              return res.json()
            })
            .then((data) => {
              if (!data.error) {
                alert('删除成功！')
                window.location.reload()
              }
            })
        }
      },
      save () {
        let info = {}
        let base = {}

        base.name = this.name[this.now] || ''
        base.intro = this.intro[this.now] || ''
        base.price = this.price[this.now] || ''
        base.serial = this.serial[this.now] || ''
        base.size = this.size
        base.material = []
        for (let i = 0; i < this.material.length; i++) {
          if (this.material[i]) {
            base.material.push(this.wood[i].name)
          }
        }

        base.images = []
        base.images.push(this.image[this.now])
        base.category = this.position

        if (this.fid > -1) {
          info.fid = this.fid
        }
        info.base = base
        info.params = this.params
        info.detail = this.content
        info.maintain = this.maintain

        let formData = new FormData()
        formData.append('info', JSON.stringify(info))

        fetch(this.fid > -1 ? `${root}backend/furniture/modified/` : `${root}backend/furniture/new/`, {
          method: 'POST',
          credentials: 'include',
          body: formData
        })
          .then((res) => {
            return res.json()
          })
          .then((data) => {
            console.log(data)
            this.hideDetail()
            window.location.reload()
          })
      },
      addInstance () {
        this.insNum++
      },
      delInstance (e) {
        let parent = e.target.parentNode.parentNode
        parent.style.display = confirm('确定删除该项吗？') ? 'none' : 'block'
        this.insNum--
      },
      handleImageAdded (file, Editor, cursorLocation) {
        let formData = new FormData()
        formData.append('image', file)

        fetch(`${root}backend/richtext/upload/`, {
          method: 'POST',
          credentials: 'include',
          body: formData
        })
          .then((res) => {
            return res.json()
          })
          .then((data) => {
            Editor.insertEmbed(cursorLocation, 'image', data.body.url)
          })
      }
    }
  }
</script>

<style scoped>
  h1 {
    font-size: 20px;
    padding: 40px 150px;
    color: #444;
  }

  select {
    margin: auto 30px;
  }

  .button {
    margin: 0 30px 30px;
    text-align: center;
  }

  .cards {
    padding: 20px 150px;
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
  }

  .card {
    margin-bottom: 60px;
    display: inline-block;
  }

  .card > div {
    display: inline-block;
    vertical-align: top;
  }

  .card > .select {
    width: 180px;
    height: 180px;
    text-align: center;
    display: inline-block;
    vertical-align: top;
  }

  .card input {
    color: #636363;
    border: none;
    width: 280px;
    display: inline-block;
    height: 28px;
    line-height: 28px;
    font-size: 20px;
    margin: 10px 10px 20px 10px;
    padding: 0 10px;
    border-bottom: 1px solid #ccc;
    outline: none;
  }

  .card .priceInput {
    width: 210px;
  }

  .card button {
    width: 30px;
    display: inline-block;
  }

  .card textarea {
    box-sizing: border-box;
    padding: 10px;
    color: #636363;
    font-size: 16px;
    margin: 0 10px;
    height: 80px;
    width: 300px;
    display: block;
    outline: none;
    line-height: 20px;
  }

  .card span {
    color: #666;
    margin: 0 10px;
  }

  .banner {
    position: relative;
  }

  .banner img {
    height: 180px;
    width: 180px;
    z-index: 11111;
  }

  .banner:after {
    content: "+";
    color: #ccc;
    line-height: 178px;
    font-size: 40px;
    font-weight: lighter;
    text-align: center;
    position: absolute;
    width: 180px;
    height: 180px;
    border: 1px #ccc dashed;
    box-sizing: border-box;
    left: 0;
    top: 0;
    z-index: -1;
  }

  .editor {
    height: 800px;
    margin-bottom: 100px;
    position: relative;
  }

  .select {
    height: 220px;
    position: relative;
    vertical-align: top;
  }

  .upload,
  .success{
    content: "上传";
    color: white;
    line-height: 30px;
    font-size: 16px;
    font-weight: lighter;
    text-align: center;
    position: absolute;
    width: 48px;
    height: 30px;
    box-sizing: border-box;
    right: 0;
    top: 0;
    z-index: 100003;
    background: rgba(0, 0, 0, 0.5);
    cursor: pointer;
  }

  .success {
    color: white;
    background: green;
    cursor: not-allowed;
  }

  .detail {
    width: 1120px;
    position: absolute;
    top: 30px;
    left: 50%;
    margin-left: -520px;
    margin-bottom: 30px;
    background: white;
    border: 1px solid #efefef;
    border-radius: 15px;
    z-index: 8;
    padding: 0 40px 40px;
    box-sizing: border-box;
    overflow: hidden scroll;
  }

  .detail h1 {
    text-align: center;
  }

  .detail p {
    margin: 15px 0;
  }

  .detail input {
    width: 960px;
    height: 50px;
    padding: 0 20px;
    font-size: 16px;
    box-sizing: border-box;
    outline: none;
    border-radius: 10px;
    border: 1px solid #efefef;
  }

  .detail textarea {
    width: 960px;
    height: 120px;
    padding: 10px 20px;
    font-size: 16px;
    box-sizing: border-box;
    outline: none;
    border-radius: 10px;
    border: 1px solid #efefef;
  }

  .detail .submit {
    margin: 50px 30px -20px;
    text-align: center;
  }

  .detail .select {
    width: 180px;
    display: inline-block;
    margin: 20px auto;
  }

  .detail .checkbox {
    width: 20px;
    height: 16px;
    text-align: center;
    display: inline-block;
    vertical-align: middle;
  }

  .detail span {
    margin-left: 20px;
  }

  .images {
    width: 960px;
    height: 240px;
  }

  .images .select {
    width: 180px;
    display: inline-block;
    margin: 20px 75px 20px 0;
  }

  .images .select:last-child {
    margin-right: 0;
  }

  .mask {
    width: 100%;
    height: 100%;
    position: fixed;
    top: 0;
    left: 0;
    background: rgba(0, 0, 0, 0.6);
    z-index: 7;
  }

  .service {
    margin-top: 50px;
  }

  .close {
    position: absolute;
    font-family: sans-serif;
    font-size: 32px;
    color: #eee;
    right: 30px;
    top: 20px;
    user-select: none;
    cursor: pointer;
  }

  .instance {
    margin-top: 100px;
  }

  .del {
    float: right;
    width: 100px;
    margin-top: 5px;
  }

  .button {
    margin: 40px 0;
  }

  .button span {
    margin: 0 20px;
  }

  @media(max-width: 1366px) {
    h1 {
      padding: 30px 120px;
    }

    .cards {
      padding: 20px 120px;
    }
  }
</style>
