<template>
  <div class="header">
  	<div class="header-content">
  		<div class="pic">
  			<img :src="seller.avatar" width="64" height="64">
  		</div>
  		<div class="content-right">
  			<div class="title">
  				<span class="brand"></span>
  				<span class="name">{{seller.name}}</span>
  			</div>
  			<div class="description">
  			{{seller.description}}/{{seller.deliveryTime}}分钟送达
  			</div>
  			<div class="supports" v-if="seller.supports">
  			  <span class="icon" v-bind:class="classMap[seller.supports[0].type]"></span>
  			  <span class="text">{{seller.supports[0].description}}</span>
  			</div>
  		</div>
  		<div v-if="seller.supports" class="support-count" @click="detailShow=true">
  			<span class="count">{{seller.supports.length}}个</span>
  			<i class="icon-keyboard_arrow_right"></i>
  		</div>
  	</div>
  	<div class="header-foot" @click="detailShow=true">
  		<span class="title"></span><span class="text">{{seller.bulletin}}</span>
  		<span class="icon-keyboard_arrow_right"></span>
  	</div>
  	<div class="background_pig">
  		<img :src="seller.avatar" width="100%" height="100%">
  	</div>
  	<div v-show="detailShow" class="detail">
      <div class="detail_wrap clearfix">
      	<div class="detail_content">
      		<div class="name">{{seller.name}}</div>
      		<div class="star-wrapper">
              <star :size="48" :score="seller.score"></star>
            </div>
            <div class="title">
            	<div class="line"></div>
            	<div class="text">优惠信息</div>
            	<div class="line"></div>
            </div>
            <div class="supports" v-if="seller.supports" v-for="(item,index) in seller.supports">
  			  <span class="icon" v-bind:class="classMap[index]"></span>
  			  <span class="text">{{item.description}}</span>
  			</div>
  			<div class="title">
            	<div class="line"></div>
            	<div class="text">商家公告</div>
            	<div class="line"></div>
            </div>
            <div class="bulletin">
            	<p class="bulletin_content">{{seller.bulletin}}</p>
            </div>
      	</div>
      </div>
      <div class="detail_close" @click="detailShow=false">
      	<div class="icon-close"></div>
      </div>
  	</div>
  </div><!-- header结束 -->
</template>

<script>
  import star from 'components/star/star2';
  export default{
   data() {
     return {
        classMap: ["decrease_1","discount_1","special_1","invoice_1","guarantee_1"],
        detailShow: false
     }
   },
   props: {
     seller: {
       type: Object
     }
   },
   components: {
      star
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";

.header
  position: relative
  color: rgb(255,255,255)
  background-color: #999
  background: rgba(7,17,27,0.5)
  overflow: hidden
  .header-content
    position: relative
    padding: 24px 12px 18px 24px
    font-size: 0
    .pic
      display: inline-block
      margin-right: 16px
      vertical-align: top
      img
        border-radius: 2px
    .content-right
      display: inline-block
      .title
        margin: 2px 0 8px
        .brand
          display: inline-block
          vertical-align: top
          width: 30px
          height: 18px
          bg-image("brand")
          background-size: 30px 18px
          background-repeat: no-repeat
          margin-right: 6px
        .name
          font-size: 16px
          font-weight: bold
          line-height: 18px
      .description
        margin-bottom: 10px
        font-size: 12px
        line-height: 12px
      .supports
        .icon
          width: 12px
          height: 12px
          margin-right: 4px
          display: inline-block
          background-size: 12px 12px
          background-repeat:no-repeat
          &.decrease_1
            bg-image("decrease_1")
          &.discount_1
            bg-image("discount_1")
          &.guarantee_1
            bg-image("guarantee_1")
          &.invoice_1
            bg-image("invoice_1")
          &.special_1
            bg-image("special_1")
        .text
          font-size: 10px
          vertical-align: top
    .support-count
      position: absolute
      right: 12px
      bottom: 14px
      height: 24px
      line-height: 24px
      padding: 0 8px
      background: rgba(0,0,0,0.2)
      text-align: center
      border-radius: 14px
      .count
        vertical-align: top
        font-size: 10px
      .icon-keyboard_arrow_right
        margin-left: 2px
        line-height: 24px
        font-size: 10px    
  .header-foot
    position: relative
    padding-right: 22px
    height: 28px
    line-height: 28px
    background-color: rgba(7,17,27,0.2)
    white-space: nowrap
    overflow: hidden
    text-overflow: ellipsis
    .title
      vertical-align: top
      margin: 9px 0 0 12px
      width: 22px
      height: 12px
      display: inline-block
      bg-image("bulletin")
      background-repeat: no-repeat
      background-size: 22px 12px
    .text
      font-size: 10px
      line-height: 28px
      margin-left: 4px
    .icon-keyboard_arrow_right
      position: absolute
      font-size: 10px
      top: 10px
      right: 12px

  .background_pig
    position: absolute
    top: 0
    left: 0
    width: 100%
    height: 100%
    z-index: -1
    filter: blur(10px)
  .detail
    position: fixed
    top: 0;
    left: 0;
    width: 100%
    height: 100%
    z-index: 100
    overflow: auto
    backdrop-filter: blur(10px)
    opacity: 1
    background: rgba(7,17,27,0.8)
    .detail_wrap
      min-height: 100%
      width: 100%
      .detail_content
        padding-bottom: 64px
        margin-top: 64px
        .name
          text-align: center
        .star-wrapper
          margin-top: 16px
          text-align: center
        .title
          display: flex
          width: 80%
          margin: 28px auto 24px auto
          .line
            flex: 1
            position: relative
            border-bottom: solid 1px rgba(255,255,255,0.2)
            bottom: 6px
          .text
            padding: 0 12px
            font-size: 14px
	    .supports
	      font-size: 0
	      width: 80%
	      margin: 0 auto
	      padding-left: 12px
	      margin-bottom: 12px
	      &:last-child
	        margin-bottom: 0
		  .icon
		    width: 16px
		    height: 16px
		    margin-right: 6px
		    display: inline-block
		    background-size: 16px 16px
		    background-repeat:no-repeat
		    &.decrease_1
		      bg-image("decrease_2")
		    &.discount_1
		      bg-image("discount_2")
		    &.guarantee_1
		      bg-image("guarantee_2")
		    &.invoice_1
		      bg-image("invoice_2")
		    &.special_1
		      bg-image("special_2")
		  .text
		    font-size: 12px
		    vertical-align: top
		    font-weight: 200
		    line-height: 16px
		  .bulletin
		    width: 80%
		    margin: 0 auto
		    .bulletin_content
		      padding: 0 12px
		      font-size: 12px
		      line-height: 24px
		      font-weight: 200
    .detail_close
      position: relative
      clear: both
      width: 32px
      height: 32px
      margin: -64px auto 0
      
</style>