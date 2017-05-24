<template>
<transition name="food">
  <div class="food" v-show="showFlag" ref="food">
  	<div class="food_content">
  	  <div class="image_header">
  	  	<img :src="food.image" class="img">
  	  	<div class="back" @click="back">
  	  	  <span class="icon-arrow_lift"></span>
  	  	</div>
  	  </div>
  	  <div class="content">
  	  	<h1 class="title">{{food.name}}</h1>
  	  	<div class="detail">
  	  	  <span class="sell_count">月售{{food.sellCount}}</span>
  	  	  <span class="rating">好评率{{food.rating}}%</span>
  	  	</div>
  	  	<div class="price">
  	  	  <span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
  	  	</div>
  	  	<dir class="cartContral-wrapper">
  	  	  <cartContral :food="food" @add="addFood"></cartContral>
  	  	</dir>
  	  	<transition name="buy">
  	  	  <div class="buy" v-show="!food.count || food.count===0" @click.stop.prevent="addFirst">加入购物车</div>
  	  	</transition>
  	  </div>
  	  <split v-show="food.info"></split>
  	  <div class="info" v-show="food.info">
  	  	<div class="title">商品信息</div>
  	  	<p class="text">{{food.info}}</p>
  	  </div>
  	  <split></split>
  	  <div class="rating">
  	  	<div class="title">商品评价</div>
  	  	<ratingselect :selectType="selectType" :onlyContent="onlyContent" :desc="desc" :ratings="food.ratings" @toggle="toggleContent" @select="select"></ratingselect>
  	  	<div class="rating_wrapper">
  	  		<ul v-show="food.ratings && food.ratings.length">
  	  		  <li class="rating_item" v-for="rating in food.ratings" v-show="needShow(rating.rateType,rating.text)">
  	  		  	<div class="user">
  	  		  		<span class="name">{{rating.username}}</span>
  	  		  		<img :src="rating.avatar" width="12" height="12">
  	  		  	</div>
  	  		  	<div class="name">{{rating.rateTime | formatDate}}</div>
  	  		  	<div class="text">
  	  		  		<span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>{{rating.text}}
  	  		  	</div>
  	  		  </li>
  	  		</ul>
  	  		<div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
  	  	</div>
  	   </div> 
  	</div>
  </div>
</transition>
</template>

<script>
import cartContral from 'components/cartContral/cartContral'
import BScroll from 'better-scroll';
import Vue from 'vue'
import split from 'components/split/split'
import ratingselect from 'components/ratingselect/ratingselect'
import {formatDate} from 'common/js/date'

var ALL = 2;

export default {
  data() {
    return {
    	showFlag: false,
    	selectType: ALL,
    	onlyContent: false,
    	desc: {
    		all: '全部',
    		positive: '推荐',
        negative: '吐槽'
    	}

    }
  },
  props: {
  	food: {
  		type: Object
  	}
  },
  filters: {
     formatDate(time) {
        var date = new Date(time);
        return formatDate(date,'yyyy-MM-dd hh:mm')
     }
  },
  methods: {
  	select(type) {
       this.selectType = type;
       this.$nextTick(() => {
       	   this.scroll.refresh();
       });
  	},
  	toggleContent() {
  		this.onlyContent = !this.onlyContent;
        this.$nextTick(() => {
        	this.scroll.refresh();
        })
  	},
  	needShow(type,text) {
       if (this.onlyContent && !text) {
       	return false;
       }
       if (this.selectType === ALL) {
       	return true;
       } else {
       	 return type === this.selectType;
       }

  	},
  	addFood(target) {
      this.$emit('add',target);
  	},
  	addFirst(event) {
  	  if (!event._constructed) {
  	  	return;
  	  } 
  	  Vue.set(this.food,'count',1);
      this.$emit('addFirst',event.target);
  	},
  	show() {
  	  this.showFlag = true;
  	  this.selectType = ALL;
  	  this.onlyContent = true;
  	  this.$nextTick(() => {
        if (!this.scroll) {
        	this.scroll = new BScroll(this.$refs.food,{
        		click: true
        	})
        } else {
        	this.scroll.refresh();
        }
  	  });
  	},
  	back() {
  	   this.showFlag = false;
  	}
  },
  components: {
  	cartContral,
  	split,
  	ratingselect
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import '../../common/stylus/mixin.styl'

.food
  position: fixed
  top: 0
  left: 0
  bottom: 48px
  z-index: 30
  width: 100%
  background: #fff
  transform: translate3d(0,0,0)
  &.food-enter-active,&.food-leave-active
    transition: all 0.5s
  &.food-enter,&.food-leave-active
    transform: translate3d(100%,0,0)
  .image_header
    position: relative
    width: 100%
    height: 0
    padding-top: 100%
    .img
      position: absolute
      top: 0
      left: 0
      width: 100%
      height: 100%
    .back
      position: absolute
      top:10px
      left: 0
      .icon-arrow_lift
        display: inline-block
        padding: 10px
        font-size: 20p
        color: #fff 
  .content
    position: relative
    padding: 18px
    .title
      line-height: 14px
      margin-bottom: 8px
      font-size: 14px
      font-weight: 700
      color: rgb(7,17,27)
    .detail
      margin-bottom: 18px
      line-height: 10px
      font-size: 0
      .sell_count, .rating
        font-size: 10px
        color: rgb(147,153,159)
      .sell_count
        margin-right: 12px

    .price
      font-weight: 700
      line-height: 24px
      .now
        margin-right: 8px
        font-size: 14px
        color: rgb(240,20,20)
      .old
        text-decoration: line-through
        font-size: 10px
        color: rgb(147,153,159)
    .cartContral-wrapper
      position: absolute
      right: 12px
      bottom: 1px
    .buy
      position: absolute
      right: 18px
      bottom: 18px
      z-index: 10
      height: 24px
      line-height: 24px
      padding: 0 12px
      box-sizing: border-box
      border-radius: 12px
      font-size: 10px
      color: #fff
      background: rgb(0,160,220)
      opacity: 1
      &.buy-enter-active,&.buy-leave-active
        transition: all 0.2s
      &.buy-enter,&.buy-leave-active
        opacity: 0
  .info
    padding: 18px
    .title
      line-height: 14px
      margin-bottom: 6px
      font-size: 14px
      color: rgb(7,17,27)
    .text
      line-height: 24px
      padding: 0 8px
      font-size: 12px
      color: rgb(77,85,93)
  .rating
    padding-top: 18px
    .title
      line-height: 14px
      margin-left: 18px
      font-size: 14px
      color: rgb(7,17,27)
    .rating_wrapper
      padding: 0 18px
      .rating_item
        position: relative
        padding: 16px 0
        border-1px(rgba(7,17,27,0.1))
        .user
          position: absolute
          right: 0
          top: 16px
          line-height: 12px
          font-size: 0
          .name
            display: inline-block
            margin-right: 6px
            vertical-align: top
            font-size: 10px
            color: rgb(147,153,159)
          .avatar
            border-radius: 50%
        .time
          margin-bottom: 6px
          line-height: 12px
          font-size: 10px
          color: rgb(147,153,159)
        .text
          line-height: 16px
          font-size: 12px
          color: rgb(7,17,27)
          .icon-thumb_up,.icon-thumb_down
            margin-right: 4px
            line-height: 16px
            font-size: 12px
          .icon-thumb_up
            color: rgb(0,160,220)
          .icon-thumb_down
            color: rgb(147,153,159)
      .no-rating
        padding: 16px 0
        font-size: 12px
        color: rgb(147,153,159)



</style>