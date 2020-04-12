<template>
  <div class="menubar">
    <transition name="menu-up">
      <div class="menu-wrapper" v-show="isshow" :class="{'hideBoxShadow':ifSettingShow||!isshow}">
        <div class="icon-wrapper">
          <span class="iconfont iconmenu icon" @click= "showCatalogue"></span>
        </div>
        <div class="icon-wrapper">
          <span class="iconfont iconprogress icon" @click="showSetting(2)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="iconfont iconbright icon" @click="showSetting(1)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="iconfont icona icon" @click="showSetting(0)"></span>
        </div>
      </div>
    </transition>
    <transition name="menu-up">
      <div class="setting-wrapper" v-show="ifSettingShow">
        <div class="setting-fontSize" v-if="showTag===0">
          <div class="preview" :style="{fontSize:fontSizeList[0].fontSize + 'px'}">A</div>
          <div class="select">
            <div class="select-wrapper"
            v-for="(item,index) of fontSizeList"
            @click="setFontSize(item.fontSize)"
            :key="index">
              <div class="line"></div>
              <div class="point-wrapper">
                <div class="point" v-show="defaultFontSize===item.fontSize">
                  <div class="small-point"></div>
                </div>
              </div>
              <div class="line"></div>
            </div>
          </div>
          <div class="preview" :style="{fontSize:fontSizeList[fontSizeList.length-1].fontSize + 'px'}">A</div>
        </div>
        <div class="setting-theme" v-else-if="showTag===1">
          <div class="setting-theme-item"
           v-for = "(item,index) of themeList"
           @click="changeTheme(index)"
           :key="index">
            <div class="preview"
            :class="{'no-border': item.style.body.background!== '#fff'}"
            :style="{background: item.style.body.background}">
          </div>
            <div class="color" :class="{'font-style':index===defaultStyle}">{{item.name}}</div>
          </div>
        </div>
        <div class="setting-progress" v-else-if="showTag===2">
          <div class="progress-wrapper">
            <input class="progress" type="range"
                                    max="100"
                                    min="0"
                                    step=".01"
                                    @change="onProgressChange($event.target.value)"
                                    @input="onProgressInput($event.target.value)"
                                    :value="progress"
                                    :disabled = "!bookAvailable"
                                    ref="progress">
          </div>
          <div class="text-wrapper">
            {{bookAvailable? progress+'%':'加载中...'}}
          </div>
        </div>
      </div>
    </transition>
    <div class="catolugue">
      <div class="content-mask" v-show="ifShowContent"></div>
      <div class="drawer-wrapper">
        <el-drawer
        title="我是标题"
        direction="ltr"
        size = "70%"
        :visible.sync="ifShowContent"
        :with-header="false">
          <ul
           v-for="(item,index) of navigation.toc"
           :key="index"
           @click= "jumpTo(item.href)">
            <li>{{item.label}}</li>
            <hr>
          </ul>
        </el-drawer>
      </div>
    </div>
  </div>
</template>

<script>
import ContentView from './contentView'
export default {
  props: {
    isshow: Boolean,
    fontSizeList: Array,
    defaultFontSize: Number,
    themeList: Array,
    defaultStyle: Number,
    bookAvailable: Boolean,
    navigation: Object
  },
  data () {
    return {
      ifSettingShow: false,
      showTag: 0,
      progress: 0,
      ifShowContent: false
    }
  },
  components: {
    ContentView
  },
  methods: {
    showCatalogue() {
      this.ifShowContent = true
      this.ifSettingShow = false
    },
    jumpTo(href) {
      this.$emit('jumpTo', href)
      this.ifShowContent = false
      this.hideSetting()
    },
    hideContent() {
      this.ifShowContent = false
    },
    onProgressInput(progress) {
      this.progress = progress
      this.$refs.progress.style.backgroundSize = `${this.progress}% 100%`
    },
    onProgressChange(progress) {
      this.$emit('onProgressChange', progress)
    },
    changeTheme (index) {
      console.log(index)
      this.$emit('setTheme', index)
    },
    showSetting (index) {
      this.ifSettingShow = true
      this.showTag = index
    },
    hideSetting () {
      this.ifSettingShow = false
    },
    setFontSize (fontSize) {
      this.$emit('setFontSize', fontSize)
    }
  }
}
</script>

<style lang="scss" scoped>
@import '../assets/style/global';
.menubar{
  .menu-wrapper{
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
    height: px2rem(48);
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0,0,0,.15);
    z-index: 101;
    display: flex;
    background: white;
    justify-content: space-around;
    &.hideBoxShadow{
      box-shadow: none;
    }
    .icon-wrapper{
      @include center;
      .iconprogress{
        font-size: px2rem(24);
      }
    }
  }
  .setting-wrapper{
    position: absolute;
    bottom: px2rem(48);
    left: 0;
    height: px2rem(60);
    width: 100%;
    background: white;
    z-index: 101;
    box-shadow:0 px2rem(-8) px2rem(8) rgba(0,0,0,.15);
    .setting-fontSize{
      display: flex;
      height: 100%;
      .preview{
        flex: 0 0 px2rem(40);
        @include center
      }
      .select{
        display: flex;
        flex: 1;
        .select-wrapper{
          flex: 1;
          display: flex;
          align-items: center;
          &:first-child{
            .line{
              &:first-child{
                border-top: none;
              }
            }
          }
          &:last-child{
            .line{
              &:last-child{
                border-top: none;
              }
            }
          }
          .line{
            flex: 1;
            height: 0;
            border-top: px2rem(1) solid #ccc;
          }
          .point-wrapper{
            flex: 0 0 0;
            width: 0;
            height: px2rem(7);
            border-left: px2rem(1) solid #ccc;
            position: relative;
            .point{
              position: absolute;
              top: px2rem(-7);
              left: px2rem(-10);
              width: px2rem(20);
              height: px2rem(20);
              border-radius: 50%;
              border: px2rem(1) #ccc solid;
              box-shadow: 0 px2rem(8) px2rem(8) rgba(0,0,0,.15);
              background: white;
              @include center;
              .small-point{
                width: px2rem(5);
                height: px2rem(5);
                border-radius: 50%;
                background: black;
              }
            }
          }
        }
      }
    }
    .setting-theme{
      height: 100%;
      display: flex;
      .setting-theme-item{
        flex: 1;
        display: flex;
        flex-direction: column;
        padding: px2rem(5);
        box-sizing: border-box;
        .preview{
          flex: 1;
          border: px2rem(1) solid #333;
          &.no-border{
            border: none
          }
        }
        .color{
          flex: 0 0 px2rem(20);
          font-size: px2rem(15);
          color: #eee;
          @include center;
          &.font-style{
            color: #333;
          }
        }
      }
    }
    .setting-progress{
      position: relative;
      width: 100%;
      height: 100%;
      font-size: px2rem(20);
      text-align: center;
      .progress-wrapper{
        width: 100%;
        // height: 100%;
        display: flex;
        .progress{
          flex: 1;
          margin: px2rem(15) px2rem(10);
          -webkit-appearance: none;
          height: px2rem(2);
          background: -webkit-linear-gradient(#999,#999) no-repeat, #ddd;
          background-size: 0 100%;
          &:focus {
            outline: none;
          }
        }
        .text-wrapper{
          font-size: px2rem(5);
        }
      }
    }
  }
  .catolugue{
    width: 100%;
    height: 100%;
    .content-mask{
      position: absolute;
      left: 0;
      top: 0;
      z-index: 101;
      display: flex;
      width: 100%;
      height: 100%;
      background: reba(51,51,51,.8);
    }
    .drawer-wrapper{
      font-size: px2rem(20)
    }
  }
}
</style>
