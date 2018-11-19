<template>
  <div class="cortcontrol">
    <transition name='fade'>
          <div class="cart-decrease icon-remove_circle_outline" @click='decrease' v-show='food.count>0'></div>
    </transition>
    <div class="cart-count" v-show='food.count>0'>{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click='addCart'></div>
  </div>
</template>

<script>
import Vue from 'vue'
export default {
  name: 'cortcontrol',
  data () {
    return {
    }
  },
  props: {
    food: {
      type: Object
    }
  },
  methods: {
    addCart (event) {
      if (!event._constructed) {
        return
      }
      if (!this.food.count) {
        Vue.set(this.food, 'count', 1)
      } else {
        this.food.count++
      }
      this.$emit('cart-add', event.target)
    },
    decrease (event) {
      if (!event._constructed) {
        return
      }
      if (this.food.count) {
        this.food.count--
      }
    }
  }
}
</script>
<style lang='stylus' scoped>
@import '../../common/stylus/icon.css'
  .fade-enter-active, .fade-leave-active
    transition: opacity .5s
    transform translate3D(0,0,0)
  .fade-enter, .fade-leave-to
    opacity: 0
    transform translate3D(24px,0,0) rotate(-180deg)
  .cortcontrol
    font-size 0
    .cart-decrease
      display inline-block
      line-height 24px
      padding 6px
      font-size 24px
      color rgb(0,160,220)
      transition all 0.4s linear
    .cart-count
      display inline-block
      vertical-align top
      width 12px
      padding-top 6px
      line-height 24px
      text-align center
      font-size 10px
      color rgb(147,153,159)
    .cart-add
      display inline-block
      line-height 24px
      padding 6px
      font-size 24px
      color rgb(0,160,220)
</style>
