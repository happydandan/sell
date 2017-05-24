<template>
  <div class="star" :class="starSize">
  	<span class="item_star" v-for="(itemClass,index) in items" :class="itemClass"></span>
  </div>
</template>

<script>
export default {
  props: {
    size: {
      type: Number
    },
    score: {
      type: Number
    }
  },
  computed: {
     starSize() {
       return 'star_' + this.size;
     },
     items() {
       var classArry = [];
       var integer = Math.floor(this.score);
       var score = Math.floor(this.score * 2) / 2;
       var half = score % 1 !== 0;
       for (var i = 0; i < integer; i++) {
         classArry.push('on');
       }
       if (half) {
         classArry.push('half');
       }
       while (classArry.length < 5) {
         classArry.push('off');
       }
       return classArry;
     }
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
 @import "../../common/stylus/mixin.styl"

 .star
   font-size: 0
   .item_star
     display: inline-block
     background-repeat: no-repeat
   &.star_48
     .item_star
       width: 20px
       height: 19px
       margin-right: 22px
       background-size: 20px 20px
       &:last-child
         margin-right: 0
       &.on
         bg-image("star48_on")
       &.half
         bg-image("star48_half")
       &.off
         bg-image("star48_off")
   &.star_36
     .item_star
       width: 15px
       height: 15px
       margin-right: 6px
       background-size: 15px 15px
       &:last-child
         margin-right: 0
       &.on
         bg-image("star36_on")
       &.half
         bg-image("star36_half")
       &.off
         bg-image("star36_off")
   &.star_24
     .item_star
       width: 10px
       height: 10px
       margin-right: 3px
       background-size: 10px 10px
       &:last-child
         margin-right: 0
       &.on
         bg-image("star24_on")
       &.half
         bg-image("star24_half")
       &.off
         bg-image("star24_off")


  
</style>