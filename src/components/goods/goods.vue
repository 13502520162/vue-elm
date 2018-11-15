<template>
 <div class="goods">
  <div class="menu-wrapper">
    <ul>
      <li class="menu-item border-bottom" style="display: table;" v-for='(item,index) of goods' :key='index'>
        <span class="text">
          <span class="icon" v-show='item.type>0' :class="classMap[item.type ]"></span>
          {{item.name}}
          </span>
      </li>
    </ul>
  </div>
  <div class="foods-wrapper"></div>
 </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'goods',
  props: {
    seller: {
      type: Object
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    this.getInfo()
  },
  data () {
    return {
      goods: []
    }
  },
  methods: {
     getInfo () {
      axios.get('/api/goods', {
      })
      .then(this.getInfoSucc)
    },
    getInfoSucc (res) {
      res = res.data
      if (res.succ && res.data) {
        const data = res.data
        this.goods = data
        console.log(data)
      }
    }
  }
}
</script>
<style lang='stylus' scoped>
  @import '../../common/stylus/mixin'
  .goods
    display flex
    position absolute
    top 174px
    bottom 46px
    width 100%
    overflow hidden
    .menu-wrapper
      flex 0 0 80px
      width 80px
      background #f3f5f7
      overflow auto
      li.menu-item
        dispaly table
        height 54px
        width 56px
        line-height 14px
        padding 0 12px
        .icon
          display inline-block
          width 12px
          height 12px
          margin-right 2px
          background-size 12px
          background-repeat no-repeat
          vertical-align bottom
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.guarantee
            bg-image('guarantee_3')
          &.invoice
            bg-image('invoice_3')
          &.special
            bg-image('special_3')
        .text
          font-size 12px
          display table-cell
          width 56px
          vertical-align middle
    .foods-wrapper
      flex 1
</style>
