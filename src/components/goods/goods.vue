<template>
 <div class="goods">
  <div class="menu-wrapper" ref="meunwrapper">
    <ul>
      <li class="menu-item border-bottom" @click='selectMenu(index,$event)' style="display: table;" v-for='(item,index) of goods' :key='index' :class="{'current':currentIndex === index }">
        <span class="text">
          <span class="icon" v-show='item.type>0' :class="classMap[item.type ]"></span>
          {{item.name}}
          </span>
      </li>
    </ul>
  </div>
  <div class="foods-wrapper" ref="foodswrapper">
    <ul>
      <li v-for="(items,index) of goods" class="food-list food-list-hook" :key='index'>
        <h1 class="title">{{items.name}}</h1>
        <ul>
          <li v-for='(item,index) of items.foods' :key='index' class="food-item border-bottom">
            <div class="icon">
              <img width="57" height="57" :src="item.icon" alt="">
            </div>
            <div class="content">
              <h2 class="name">{{item.name}}</h2>
              <p class="desc">{{item.description}}</p>
              <div class="extra">
                <span class="count">月售{{item.sellCount}}份</span>
                <span>好评率{{item.rating}}%</span>
              </div>
              <div class="price">
                <span class="now">￥{{item.price}}</span>
                <span class="old" v-show='item.oldPrice'>￥{{item.oldPrice}}</span>
              </div>
              <div class="coracontrol-wraper">
                <cortcontrol @cart-add="cartAdd" :food='item'></cortcontrol>
              </div>
            </div>
          </li>
        </ul>
      </li>
    </ul>
  </div>
  <shopcart  ref="shopcart" :select-foods='selectFoods' :delivery-price='seller.deliveryPrice' :min-price='seller.minPrice'></shopcart>
 </div>
</template>

<script>
import axios from 'axios'
import BScroll from 'better-scroll'
import shopcart from 'components/shopcart/shopcart'
import cortcontrol from 'components/cortcontrol/cortcontrol'
export default {
  name: 'goods',
  props: {
    seller: {
      type: Object
    }
  },
  components: {
    shopcart,
    cortcontrol
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
  },
  mounted () {
    this.getInfo()
  },
  data () {
    return {
      goods: [],
      listHeight: [],
      scrollY: 0
    }
  },
  computed: {
    currentIndex () {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height = this.listHeight[i]
        let height1 = this.listHeight[i + 1]
        if (!height1 || (this.scrollY >= height && this.scrollY < height1)) {
          return i
        }
      }
      return 0
    },
    selectFoods () {
      let foods = []
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if (food.count) {
            foods.push(food)
          }
        })
      })
      return foods
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
        this.initScroll()
        this.$nextTick(() => {
          this.calculateHeigth()
        })
      }
    },
    initScroll () {
      this.meunScroll = new BScroll(this.$refs.meunwrapper, {
        click: true
      })
      this.foodScroll = new BScroll(this.$refs.foodswrapper, {
        probeType: 3,
        click: true
      })
      this.foodScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y))
      })
    },
    calculateHeigth () {
      let foodList = this.$refs.foodswrapper.getElementsByClassName('food-list-hook')
      let height = 0
      this.listHeight.push(height)
      for (let i = 0; i < foodList.length; i++) {
        let item = foodList[i]
        height += item.clientHeight
        this.listHeight.push(height)
      }
    },
    selectMenu (index, event) {
      if (!event._constructed) {
        return
      }
      let foodList = this.$refs.foodswrapper.getElementsByClassName('food-list-hook')
      let el = foodList[index]
      this.foodScroll.scrollToElement(el, 300)
    },
    cartAdd (el) {
      this.$refs.shopcart.drop(el)
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
      li.menu-item
        dispaly table
        height 54px
        width 56px
        line-height 14px
        padding 0 12px
        &.current
          position relative
          margin-top 1px
          z-index 10
          background #fff
          font-weight 700
          .text
            border none
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
      .title
        padding-left 14px
        height 16px
        line-height 16px
        border-left 2px soild #d9dde1
        font-size 12px
        color rgb(147,153,159)
        background #f3f5f7
      .food-item
        display flex
        margin 18px
        padding-bottom 18px
        &:last-child
          border-bottom none
          margin-bottom 0
        .icon
          flex 0 0 57px
          margin-right 10px
        .content
          flex 1
          .name
            font-size 14px
            margin 2px 0 8px 0
            height 14px
            line-height 14px
            color rgb(7,17,27)
          .desc,.extra
            line-height 10px
            font-size 10px
            color rgb(147,153,159)
          .desc
            margin-bottom 8px
            line-height 12px
          .extra
            .count
              margin-right 12px
          .price
            font-weight 700
            line-height 24px
            .now
              margin-right 18xp
              font-size 14px
              color rgb(240,20,20)
            .old
              text-direction line-through
              font-size 10px
              color rgb(147,153,159)
          .coracontrol-wraper
            position absolute
            right 0
            bottom 5px
</style>
