<template>
<div>
  <div class="goods">
  <div class="goods_left" ref="leftWrapper">
    <ul>
      <li v-for="(item,index) in goods" class="li_item" :class="{'currentItem':index==currentIndex}" @click="getIndex(index,$event)">
        <span class="text">
          <span v-show="item.type>0" class="icon" :class="classArry[item.type]"></span>{{item.name}}
        </span>
      </li>
    </ul>
  </div>
  <div class="goods_right" ref="rightWrapper">
    <ul>
      <li v-for="item in goods" class="food_list" ref="food_list">
        <h1 class="title">{{item.name}}</h1>
        <ul>
          <li v-for="food in item.foods" class="food_item" @click="select(food,$event)">
            <div class="icon">
              <img :src="food.icon" width="57" height="57">
            </div>
            <div class="content">
              <div class="name">{{food.name}}</div>
              <div class="description">{{food.description}}</div>
              <div class="extra">
                <span class="sellCount">月售{{food.sellCount}}份</span><span class="rating">好评率{{food.rating}}%</span>
              </div>
              <div class="price">
                <span class="nowprice"><span class="money">￥</span>{{food.price}}</span><span class="oldPrice" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
              </div>
              <div class="cartContral_wrapper">
                <cartContral :food="food" @add="addFood"></cartContral>
              </div>
            </div>
          </li>
        </ul>
      </li>
    </ul>
  </div>
  <shopCart :seller="seller" :selectFood="selectFood" ref="shopCart"></shopCart>
</div>
<food :food="selectedFood" ref="food" @addFirst="addFood" @add="addFood"></food>  
</div>
</template>

<script>
import BScroll from 'better-scroll';
import shopCart from 'components/shopCart/shopCart.vue';
import cartContral from 'components/cartContral/cartContral.vue';
import food from 'components/food/food'

  export default {
  	data() {
  	  return {
        goods: [],
        classArry: ['decrease', 'discount', 'special', 'invoice', 'guarantee'],
        listHeight: [],
        heightY: 0,
        selectedFood: {}
  	  }
  	},
  	props: {
  	  seller: {
  	  	type: Object
  	  }
  	},
  	created() {
  	  this.$http.get('/api/goods').then((res) => {
        res = res.body;
        if (res.errno === 0) {
          this.goods = res.data;
          this.$nextTick(() => {
             this.initScroll();
             this.getListHeight();
          });
        } 
  	  });
  	},
  	methods: {
      select(food,event) {
         if (!event._constructed) {
           return;
         }
         this.selectedFood = food;
         this.$refs.food.show();
      },
      initScroll() {
      	this.goodsLeft = new BScroll(this.$refs.leftWrapper, {
            click: true
      	});
  		this.goods_right = new BScroll(this.$refs.rightWrapper, {
  	      click: true,
  	      probeType: 3
  		}); 
        this.goods_right.on('scroll',(pos) => {
           this.heightY = Math.abs(pos.y);
        });
      },
      getListHeight() {
         var foodList = this.$refs.food_list;
         var height = 0;
         this.listHeight.push(height);
         for (var i = 0; i < foodList.length; i++) {
         	height += foodList[i].clientHeight;
         	this.listHeight.push(height);
         }
      },
      getIndex(index,event) {
      	if (!event._constructed) {
      	  return;
      	}
      	var foodList = this.$refs.food_list;
      	var el = foodList[index];
      	this.goods_right.scrollToElement(el,300);
      },
      addFood(target) {
        this._dorp(target);
      },
      _dorp(target) {
        // 体验优化,异步执行下落动画
        this.$nextTick(() => {
           this.$refs.shopCart.drop(target);
        })
      }
  	},
    computed: {
      currentIndex() {
         for (var i = 0; i < this.listHeight.length; i++ ) {
         	var height1 = this.listHeight[i];
         	var height2 = this.listHeight[i + 1];
         	if (this.heightY >= height1 && this.heightY < height2 || !height2) {
               return i;
         	}
         }
      },
      selectFood() {
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food);
            }
          });
        });
        return foods;
      }
    },
    components: {
      shopCart,
      cartContral,
      food
    }
  }; 
</script>

<style lang="stylus" rel="stylesheet/stylus">
 @import "../../common/stylus/mixin.styl"
  .goods
    display: flex
    position: absolute
    top: 174px
    bottom: 46px
    width: 100%
    overflow: hidden
    .goods_left
      flex: 0 0 80px
      width: 80px
      background: #f3f5f7
      .li_item
        display: table
        height: 54px
        width: 56px
        line-height: 14px
        padding: 0 12px
        &.currentItem
          position: relative
          background: #fff
          font-weight: 700
          margin-top: -1px
          .text
            border-none()
        .icon
          display: inline-block
          width: 12px
          height: 12px
          margin-right: 2px
          vertical-align: top
          background-size: 12px 12px
          background-repeat: no-repeat
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
          font-size: 12px
          font-weight: 200
          display: table-cell
          vertical-align: middle
          border-1px(rgba(7,17,27,0.1))
    .goods_right
      flex: 1
      .title
        font-size: 12px
        line-height: 26px
        height: 26px
        color: rgb(147,153,159) 
        background: #f3f5f7
        border-left: 2px solid #d9dde1
        padding-left: 14px
      .food_item
        display: flex
        margin: 18px
        padding-bottom: 18px
        border-1px: (rgba(7,17,27,0.1))
        &:last-child
          margin-bottom: 0
          bored-none()
        .icon
          flex: 0 0 57px
          margin-right: 10px
        .content
          flex: 1
          .name
            font-size: 14px
            margin: 2px 0 8px
            color: rgb(7,17,27)
            line-height: 14px
          .description
            font-size: 10px
            line-height: 12px
            color: rgb(147,153,159)
            margin-bottom: 6px
          .extra
            font-size: 10px
            line-height: 10px
            color: rgb(147,153,159)
            margin-bottom: 4px
            .sellCount
              margin-right: 8px
          .price
            line-height: 24px
            font-weight: 700
            .nowprice
              color: rgb(240,20,20)
              margin-right: 8px
              .money
                font-size: 10px
            .oldPrice
              color: rgb(147,153,159)
              text-decoration: line-through
              font-size: 10px
          .cartContral_wrapper
            position: absolute
            right: 0
            bottom: 12px
</style>