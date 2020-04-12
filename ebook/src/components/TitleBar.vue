<template>
  <transition name="silde-down">
      <div class="title-wrapper" v-show="isshow">
        <div class="left">
          <span class="iconfont iconback icon"></span>
        </div>
        <div class="right">
          <div class="icon-wrapper">
            <span class="iconfont iconcart icon"></span>
          </div>
          <div class="icon-wrapper">
            <span class="iconfont iconPerson icon"></span>
          </div>
          <div class="icon-wrapper">
            <span class="iconfont iconmore icon"></span>
          </div>
        </div>
      </div>
    </transition>
</template>

<script>
export default {
  props: {
    isshow: Boolean
  }
}
</script>

<style lang="scss" scoped>
@import '../assets/style/global';
.title-wrapper{
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: px2rem(48);
    box-shadow: 0 px2rem(8) px2rem(8) rgba(0,0,0,.15);
    z-index: 101;
    display: flex;
    background: white;
    .left{
      flex: 0 0 px2rem(60);
      @include center;
    }
    .right{
      @include center;
      flex: 1;
      display: flex;
      justify-content: flex-end;
      .icon-wrapper{
        flex: 0 0 px2rem(40);
        @include center;
      }
    }
  }
</style>
