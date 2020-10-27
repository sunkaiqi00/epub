<template>
  <!-- 顶部菜单 -->
  <transition name="slide-down">
    <div class="top-menu-wrapper" v-show="ifTitleAndMenu">
      <div class="left">
        <div class="iconfont icon-back"></div>
      </div>
      <div class="right">
        <div class="icon-wrapper">
          <div class="iconfont icon-cart"></div>
        </div>
        <div class="icon-wrapper">
          <div class="iconfont icon-person"></div>
        </div>
        <div class="icon-wrapper">
          <div class="iconfont icon-more"></div>
        </div>
     </div>
   </div>
  </transition>
</template>
<script>
export default {
  props:{
    ifTitleAndMenu:{
        type:Boolean,
        default:false
    }
  }
}
</script>
<style scoped lang='scss'>
@import '@/assets/style/global.scss';
.top-menu-wrapper{
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: px2rem(48);
  z-index: 101;
  background: white;
  box-shadow: 0 px2rem(8) px2rem(8) rgba(0,0,0,.15);
  display: flex;
  .left{
    flex: 0 0 px2rem(60);
    @include center
  }
  .right{
    flex: 1;
    display: flex;
    justify-content: flex-end;
    align-items: center;
    .icon-wrapper{
      flex: 0 0 px2rem(40);
    }
  }
}
</style>