<template>
  <div class="shopcart">
    <div class="content" @click='toggleList'>
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class="{'highligh':totalCount>0}">
            <span class="icon-shopping_cart" :class="{'highligh':totalCount>0}"></span>
          </div>
          <div class="num" v-show='totalCount>0'>{{totalCount}}</div>
        </div>
        <div class="price" :class="{'highligh':totalPrice>0}">￥{{totalPrice}}</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right">
        <div class="pay" :class="payClass">{{payDesc}}</div>
      </div>
    </div>
    <div class="ball-container">
       <div v-for="(ball,index) in balls" :key='index'>
            <transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop">
                <div class="ball" v-show="ball.show">
                    <div class="inner inner-hook"></div>
                </div>
            </transition>
        </div>
    </div>
    <transition name='fold'>
      <div class="showcart-list" v-show='listShow'>
        <div class="list-header">
          <h1 class="title">购物车</h1>
          <span class="empty">清空</span>
        </div>
        <div class="list-content">
          <ul>
            <li class="food" v-for='(food,index) of selectFoods' :key='index'>
              <span class="name">{{food.name}}</span>
              <div class="price">
                <span>￥{{food.price*food.count}}</span>
              </div>
              <div class="cortcontrol-wrapper">
                <cortcontrol :food="food"></cortcontrol>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import cortcontrol from 'components/cortcontrol/cortcontrol'
export default {
  name: 'default',
  props: {
    selectFoods: {
      type: Array,
      default () {
        return []
      }
    },
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    }
  },
  components: {
    cortcontrol
  },
  computed: {
    totalPrice () {
      let total = 0
      this.selectFoods.forEach((food) => {
        total += food.price * food.count
      })
      return total
    },
    totalCount () {
      let count = 0
      this.selectFoods.forEach((food) => {
        count += food.count
      })
      return count
    },
    payDesc () {
      if (this.totalPrice === 0) {
        return `￥${this.minPrice}元起送`
      } else if (this.totalPrice < this.minPrice) {
        let diff = this.minPrice - this.totalPrice
        return `还差￥${diff}元起送`
      } else {
        return '去结算'
      }
    },
    payClass () {
      if (this.totalPrice < this.minPrice) {
        return 'not-enough'
      } else {
          return 'enough'
      }
    },
    listShow () {
      if (!this.totalCount) {
        return false
      }
      if (this.totalCount > 0 && !this.fold) {
        return true
      }
      return false
    }
  },
  data () {
    return {
       balls: [
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        }
      ],
      dropBalls: [],
      fold: true
    }
  },
  methods: {
    additem (event) {
      this.drop(event.target)
      this.count++
    },
    toggleList () {
      if (!this.totalCount) {
        return
      }
      this.fold = !this.fold
    },
    drop (el) {
      for (let i = 0; i < this.balls.length; i++) {
       let ball = this.balls[i]
       if (!ball.show) {
         ball.show = true
         ball.el = el
         this.dropBalls.push(ball)
         return
       }
      }
    },
    beforeDrop (el) { // 购物车小球动画实现
      let count = this.balls.length
      while (count--) {
          let ball = this.balls[count]
          if (ball.show) {
            let rect = ball.el.getBoundingClientRect() // 元素相对于视口的位置
            let x = rect.left - 32
            let y = -(window.innerHeight - rect.top - 22) // 获取y
            el.style.display = ''
            el.style.webkitTransform = 'translateY(' + y + 'px)' // translateY
            el.style.transform = 'translateY(' + y + 'px)'
            let inner = el.getElementsByClassName('inner-hook')[0]
            inner.style.webkitTransform = 'translateX(' + x + 'px)'
            inner.style.transform = 'translateX(' + x + 'px)'
          }
      }
    },
    dropping (el, done) { // 重置小球数量  样式重置
      /* eslint-disable no-unused-vars */
      let rf = el.offsetHeight
      el.style.webkitTransform = 'translate3d(0,0,0)'
      el.style.transform = 'translate3d(0,0,0)'
      let inner = el.getElementsByClassName('inner-hook')[0]
      inner.style.webkitTransform = 'translate3d(0,0,0)'
      inner.style.transform = 'translate3d(0,0,0)'
      el.addEventListener('transitionend', done)
    },
    afterDrop (el) { // 初始化小球
      let ball = this.dropBalls.shift()
      if (ball) {
        ball.show = false
        el.style.display = 'none'
      }
    }
  },
  watch: {
    selectedFoods (newFoods, oldFoods) {
      if (newFoods.length === 0) {
        this.fold = true
      }
    }
  }
}
</script>
<style lang='stylus' scoped>
@import '../../common/stylus/icon.css'
  .shopcart
    position fixed
    left 0
    bottom 0
    z-index 50
    width 100%
    height 48px
    .content
      display flex
      background #141d27
      font-size 0
      .content-left
        flex 1
        .logo-wrapper
          display inline-block
          position relative
          top -10px
          margin 0 12px
          padding 6px
          width 56px
          height 56px
          box-sizing border-box
          vertical-align top
          border-radius 50%
          background #141d27
          .logo
            width 100%
            height 100%
            border-radius 50%
            background #2b343c
            text-align center
            &.highligh
              background rgb(0,160,220)
            .icon-shopping_cart
              font-size 24px
              color #80858a
              line-height 44px
              &.highligh
                color #fff
          .num
            position absolute
            top 0
            right 0
            width 24px
            height 16px
            line-height 16px
            text-align center
            border-radius 16px
            font-size 9px
            font-weight 400
            color #ffffff
            background rgb(240,20,20)
            box-shadow 0 4px 8px rgba(0,0,0,0.4)
        .price
          display inline-block
          line-height 24px
          font-size 16px
          font-weight 700
          margin-top 12px
          padding-right 12px
          color rgba(255,255,255,.4)
          border-right 1px solid rgba(255,255,255,.1)
          box-sizing border-box
          vertical-align top
          &.highligh
            color #fff
        .desc
          display inline-block
          vertical-align top
          line-height 24px
          margin 12px 0 0 12px
          font-size 14px
          font-weight 700
          color rgba(255,255,255,.4)
      .content-right
        flex 0 0 105px
        width 105px
        .pay
          height 48px
          line-height 48px
          text-align center
          font-size 12px
          color rgba(255,255,255,.4)
          font-weight 700
          background #2b333b
          &.not-enough
            background #2b333b
          &.enough
            background #00b43c
            color #fff
    .ball-container
     position: fixed
     top: 300px
     left: 40px
    .ball
      position: fixed
      left: 32px
      bottom: 22px
      z-index: 200
      transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
      .inner
        width: 16px
        height: 16px
        border-radius: 50%
        background-color: rgb(0,160,220)
        transition: all 0.4s linear
    .showcart-list
      position absolute
      top 0
      left 0
      z-index -1
      width 100%
      transition all 0.5s
      transform translate3d(0,-100%,0)
      &.fold-enter,&.fold-leave
        transition all 0.5s
        transform translate3d(0,0,0)
    
</style>
