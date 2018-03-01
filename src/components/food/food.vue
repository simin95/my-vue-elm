<template>
    <transition name="move">
      <div class="food" v-show="showFlag" ref="food">
        <div class="image-header">
          <img :src="food.image">
          <div class="back" @click="hide">
            <i class="icon-arrow_lift"></i>
          </div>
        </div>
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail">
            <span class="sell-count">月售{{food.sellCount}}</span>
            <span class="rating">好评率{{food.rating}}%</span>
          </div>
          <div class="price">
            <span class="now">￥{{food.price}}</span>
            <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cartcontrol @add="addFood" :food="food"></cartcontrol>
          </div>
          <transition name="buy">
            <div class="buy" @click.stop.prevent="addFirst(food,$event)" v-show="!food.count || food.conut===0">加入购物车</div>
          </transition>
        </div>
        <split></split>
      </div>
    </transition>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import cartcontrol from '../cartcontrol/cartcontrol';
  import Vue from 'vue';
  import split from '../split/split'

  export default {
    name: '',
    components: {
      cartcontrol,
      split
    },
    props: {
      food: {
        type: Object
      }
    },
    data () {
      return {
        showFlag: false
      }
    },
    methods: {
      // 隐藏及显示food组件，简单好用的写法
      show() {
        this.showFlag = true;
        // 必须在下一个事件循环内计算与DOM相关的属性，否则可能会因为还未渲染而计算错误
        this.$nextTick(() => {
          // 因需要多次渲染，故判断是否存在，存在直接刷新，不存在要先new一个
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.food,{click: true});
          }
          else {
            this.scroll.refresh();
          }
        })
      },
      hide() {
        this.showFlag= false;
      },
      addFirst(food,event) {
        // 给cartcontrol添加一个当前food，并把“加入购物车”隐藏起来
        // 注意还要添加food减至0时显示“加入购物车”
        if (!event._constructed)
          return;
        // 向父组件派发add事件，用于传给good组件触发小球事件
        this.$emit('add', event.target);
        // 这是否是全局设置任意变量属性的方法？？
        Vue.set(this.food, 'count', 1)
      },
      // 因为这里是food组件里的cartfood向父组件派发add时间，没有冒泡？？,故在这里接受并继续向父组件good组件传递
      addFood(target) {
        this.$emit('add', target)
      }
    },
    computed:{

    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "food.styl";

</style>