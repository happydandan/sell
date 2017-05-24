<template>
<div class="shopCar">
   <div class="content">
   	 <div class="content_left" @click="toggleList">
   	 	<div class="logo_wrapper">
   	 		<div class="logo" :class="{'highLight':totalPrice>0}">
   	 			<span class="icon-shopping_cart"></span>
   	 		</div>
   	 		<div class="totalCount" v-show="totalCount>0">{{totalCount}}</div>
   	 	</div>
   	 	<div class="money" :class="{'highLight':totalPrice>0}">￥{{totalPrice}}</div>
   	 	<div class="shippingFee">另需配送费￥{{seller.deliveryPrice}}</div>
   	 </div>
   	 <div class="content_right" @click.stop.prevent="pay">
   	 	<span class="content" :class="{'highLight': totalPrice>=seller.minPrice}">{{content_text}}</span>
   	 </div>
   </div>
   <div class="ball_container">
      <div v-for="ball in balls">
        <transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop">
          <div class="ball" v-show="ball.show">
            <div class="inner inner-hook" ></div>
          </div>
        </transition>
      </div>
   </div>
   <transition name="fold">
     <div class="shopcart_list" v-show="listShow">
       <div class="list_header">
         <h1 class="title">购物车</h1>
         <span class="empty" @click="clearCount">清空</span>
       </div>
       <div class="listContent" ref="listContent">
         <ul>
           <li class="food" v-for="food in selectFood">
             <span class="name">{{food.name}}</span>
             <div class="price">
               <span>￥{{food.price*food.count}}</span>
             </div>
             <div class="cartContral_wrapper">
               <cartContral :food="food" @add="addFood"></cartContral>
             </div>
           </li>
         </ul>
       </div>
     </div>
   </transition>
   <transition name="mask">
     <div class="list_mask" v-show="listShow" @click="hideFold"></div>
   </transition>
</div>
</template>

<script>
 import cartContral from 'components/cartContral/cartContral.vue';
 import BScroll from 'better-scroll';

export default {
  data() {
    return {
      balls: [
        {show: false},
        {show: false},
        {show: false},
        {show: false},
        {show: false}
      ],
      dropBalls: [],
      fold: true
    }
  },
  created() {
  },
  props: {
  	seller: {
  		type: Object
  	},
  	selectFood: {
        type: Array,
        default() {
          return [];
        }
  	}
  },
  methods: {
    pay() {
      if (this.totalPrice < this.seller.minPrice) {
        return
      } else {
        alert("支付" + this.totalPrice + '元')
      }
    },
    hideFold() {
      this.fold = true;
    },
    initScroll() {
      this.listContent = new BScroll(this.$refs.listContent,{
         click: true
      })
    },
    drop(el) {
       for (var i = 0; i < this.balls.length; i++) {
        var ball = this.balls[i];
        if (!ball.show) {
          ball.show = true;
          ball.el = el;
          this.dropBalls.push(ball);
          return;
        }
       }
    },
    beforeDrop(el) {
      let count = this.balls.length;
      while (count--) {
        let ball = this.balls[count];
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect();
          let x = rect.left - 32;
          let y = -(window.innerHeight - rect.top - 22);
          el.style.display = '';
          el.style.webkitTransform = `translate3d(0,${y}px,0)`;
          el.style.transform = `translate3d(0,${y}px,0)`;
          let inner = el.getElementsByClassName('inner-hook')[0];
          inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
          inner.style.transform = `translate3d(${x}px,0,0)`;
        }
      }
    },
    dropping(el,done) {
        /* eslint-disable no-unused-vars */
        let rf = el.offsetHeight;
        this.$nextTick(() => {
          el.style.webkitTransform = 'translate3d(0,0,0)';
          el.style.transform = 'translate3d(0,0,0)';
          let inner = el.getElementsByClassName('inner-hook')[0];
          inner.style.webkitTransform = 'translate3d(0,0,0)';
          inner.style.transform = 'translate3d(0,0,0)';
          el.addEventListener('transitionend', done);
        });
    },
    afterDrop(el) {
        let ball = this.dropBalls.shift();
        if (ball) {
          ball.show = false;
          el.style.display = 'none';
        }
    },
    toggleList() {
      if (!this.totalCount) {
        return;
      }
      this.fold = !this.fold;
    },
    addFood(target) {
      this.dorp(target);
    },
    clearCount() {
      this.selectFood.forEach((food) => {
        food.count = 0;
      })
    }
  },
  computed: {
  	totalPrice() {
  	  var totalPrice = 0;
  	  this.selectFood.forEach((food) => {
         totalPrice += food.count * food.price;
  	  });
  	  
  	  return totalPrice;
  	},
  	totalCount() {
  	  var count = 0;
  	  this.selectFood.forEach((food) => {
  	  	count += food.count;
  	  });
  	  return count;
  	},
  	content_text() {
  	  if (this.totalPrice === 0) {
  	  	return '￥' + this.seller.minPrice + '起送';
  	  } else if (this.totalPrice < this.seller.minPrice) {
  	  	return '还差￥' + (this.seller.minPrice - this.totalPrice) + '元起送';
  	  } else {
  	  	return '去结算';
  	  }
  	},
    listShow() {
     if (!this.totalCount) {
          this.fold = true;
          return false;
        }
        let show = !this.fold;
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs.listContent, {
                click: true
              });
            } else {
              this.scroll.refresh();
            }
          });
        }
        return show;
      }
  },
  components: {
    cartContral
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
.shopCar
  position: fixed
  bottom: 0
  left: 0
  z-index: 50
  width: 100%
  height: 48px
  .content
    display: flex
    background: #141d27
    font-size: 0
    .content_left
      flex: 1
      vertical-align: top
      .logo_wrapper
        display: inline-block
        position: relative
        top: -10px
        margin: 0  12px
        padding: 6px
        width: 56px
        height: 56px
        box-sizing: border-box
        border-radius: 50%
        background: #141d27
        .totalCount
          position: absolute
          top: 0 
          right: 0
          font-size: 9px
          font-weight: 700
          color: rgb(255,255,255)
          line-height: 16px
          background: rgb(240,20,20)
          border-radius: 16px
          width: 24px
          text-align: center
          box-shadow: 0 4px 8px 0 rgba(0,0,0,0.4)
        .logo
          width: 100%
          height: 100%
          background: #2b343c
          border-radius: 50%          
          text-align: center
          &.highLight
            background: rgb(0,160,220)
            .icon-shopping_cart
              color: rgb(255,255,255)
          .icon-shopping_cart
            font-size: 24px
            color: rgba(255,255,255,0.4) 
            line-height: 44px
      .money
        display: inline-block
        font-size: 16px
        vertical-align: top
        line-height: 24px
        margin-top: 12px
        font-weight: 700
        color: rgba(255,255,255,0.4)
        padding-right: 12px
        border-right: 1px solid rgba(255,255,255,0.1)
        &.highLight
          color: rgb(255,255,255) 
      .shippingFee
        display: inline-block
        font-size: 14px
        vertical-align: top
        line-height: 24px
        margin-top: 12px
        color: rgba(255,255,255,0.4)
        padding: 0 12px
    .content_right
      flex: 0 0 105px
      width: 105px
      .content
        display: inline-block
        width: 105px
        font-size: 12px
        height: 48px
        line-height: 48px
        font-weight: 700
        color: rgba(255,255,255,0.4)
        text-align: center
        background: #2b333b
        &.highLight
          color: #fff
          background: #089B0B
  .ball_container
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
        background: rgb(0,160,220)
        transition: all 0.4s linear
  .shopcart_list
    position: absolute
    left: 0
    top: 0
    z-index: -1
    width: 100%
    transform: translate3d(0,-100%,0)
    &.fold-enter-active,&.fold-leave-active
      transition: all 0.5s
    &.fold-enter,&.fold-leave-active
      transform: translate3d(0,0,0)
    .list_header
      height: 40px
      line-height: 40px
      padding: 0 18px
      background: #f3f5f7
      border-bottom: 1px solid rgba(7,17,27,0.1)
      .title
        float: left
        font-size: 14px
        color: rgb(7,17,27)
      .empty
        float: right
        font-size: 12px
        color: rgb(0,160,220)
    .listContent
      padding: 0 18px
      max-height: 200px
      overflow: hidden
      background: #fff
      .food
        position: relative
        padding: 12px 0
        box-sizing: border-box
        border-1px(raba(7,17,27,0.1))
        .name
          line-height: 24px
          font-size: 14px
          color: rgb(7,17,27)
        .price
          position: absolute
          right: 90px
          bottom: 12px
          line-height: 24px
          font-size: 14px
          font-weight: 700
          color: rgb(240,20,20)
        .cartContral_wrapper
          position: absolute
          right: 0 
          bottom: 12px
.list_mask
  position: fixed
  top: 0
  left: 0
  width: 100%
  height: 100%
  z-index: -2
  background: rgba(7,17,27,0.6)
  opacity: 1
  &.mask-enter-active,&.mask-leave-active
    transition: all 0.5s
  &.mask-enter,&.mask-leave-active
    opacity: 0


</style>