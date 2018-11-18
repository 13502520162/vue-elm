<template>
  <div class="app">
    <home-header :seller='seller'></home-header>
    <div class="tab border-bottom">
      <router-link tag="div" class="tab-item"  to='/goods'>商品</router-link>
      <router-link tag="div" class="tab-item"  to='/ratings'>评价</router-link>
      <router-link tag="div" class="tab-item"  to='/seller'>商家</router-link>
    </div>
    <router-view :seller='seller'></router-view>
  </div>
</template>

<script>
import HomeHeader from './components/header/header'
import axios from 'axios'
export default {
  data () {
    return {
      seller: {}
    }
  },
  name: 'App',
  components: {
    HomeHeader
  },
  methods: {
    getInfo () {
      axios.get('/api/seller', {
      })
      .then(this.getInfoSucc)
    },
    getInfoSucc (res) {
      res = res.data
      if (res.succ && res.data) {
        const data = res.data
        this.seller = data
        console.log(data)
      }
    }
  },
  created () {
    this.getInfo()
  }
}
</script>

<style lang='stylus' scoped>
  .app
    .tab
      display flex
      width 100%
      height 40px
      line-height 40px
      .tab-item
        flex 1
        text-align center
        font-size 14px
        color rgb(77,85,93)
      .active
        color rgb(240,20,20)
</style>
