<template>
<div class="cartContral">
  <transition name="move">
  	<div class="minus" @click.stop.prevent="decreaseCount($event)" v-show="food.count>0">
  		<span class="inner icon-remove_circle_outline"></span>
  	</div>
  </transition>
  <div class="buyNumber" v-show="food.count>0">{{food.count}}</div>
  <div class="add  icon-add_circle" @click.stop.prevent="addCount($event)"></div>
</div>
</template>

<script>
 import Vue from 'vue'

export default {
  props: {
  	food: {
  		type: Object
  		}
  },
  created() {
  },
  methods: {
    decreaseCount(event) {
    	if (!event._constructed) {
    		return;
    	}    	
    	if (this.food.count > 0) {
    		this.food.count--;
    	}
    },
    addCount(event) {
    	if (!event._constructed) {
    		return
    	}
    	if (!this.food.count) {
    	  Vue.set(this.food,"count",1);
    	} else {
    		this.food.count++;
    	}
        this.$emit('add',event.target)
    }
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
.cartContral
  font-size: 0
  .minus
    display: inline-block
    padding: 0 6px
    opacity: 1
    transform: translate3d(0,0,0)
    .inner
      display: inline-block
      font-size: 24px
      line-height: 24px
      color: rgb(0,160,220)
      transition: all 0.4s linear
    &.move-enter-active, &.move-leave-active
      transition: all 0.4s linear
    &.move-enter, &.move-leave-active
      opacity: 0
      transform: translate3d(24px,0,0)
      .inner
        transform: rotate(180deg)
  .buyNumber
    display: inline-block
    vertical-align: top
    padding-top: 6px
    width: 12px
    text-align: center
    font-size: 10px
    color: #93999f
  .add
    display: inline-block
    font-size: 24px
    color: #16A7C9
    line-height: 24px
    padding: 0 6px

</style>