<template>
  <div class="ebook">
    <title-bar :isshow = "isshow"></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="titleAndMenu"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar :isshow = "isshow"
    :fontSizeList = "fontSizeList"
    :defaultFontSize = "defaultFontSize"
    @setFontSize = "setFontSize"
    :themeList = "themeList"
    :defaultStyle = "defaultStyle"
    @setTheme = "setTheme"
    :bookAvailable = "bookAvailable"
    @onProgressChange = "onProgressChange"
    :navigation = "navigation"
    @jumpTo = "jumpTo"
    ref="menubar">
    </menu-bar>
  </div>
</template>

<script>
import Epub from 'epubjs'
import TitleBar from './components/TitleBar'
import MenuBar from './components/MenuBar'
const DOWNLOAD_URL = '/static/1.epub'
global.epub = Epub
export default {
  data () {
    return {
      isshow: false,
      fontSizeList: [
        {fontSize: 12},
        {fontSize: 14},
        {fontSize: 16},
        {fontSize: 18},
        {fontSize: 20},
        {fontSize: 22},
        {fontSize: 24}
      ],
      defaultStyle: 0,
      defaultFontSize: 16,
      // 图书是否为可用状态
      bookAvailable: false,
      progress: 0,
      themeList: [{
        name: 'default',
        style: {
          body: {
            'color': '#000',
            'background': '#fff'
          }
        }
      }, {
        name: 'eye',
        style: {
          body: {
            'color': '#000',
            'background': '#ceeaba'
          }
        }
      }, {
        name: 'night',
        style: {
          body: {
            'color': '#fff',
            'background': '#000'
          }
        }
      }, {
        name: 'gold',
        style: {
          body: {
            'color': '#000',
            'background': 'rgb(241,236,226)'
          }
        }
      }]
    }
  },
  components: {
    TitleBar,
    MenuBar
  },
  methods: {
    // 根据链接跳转到指定位置
    jumpTo(href) {
      this.rendition.display(href)
      this.titleAndMenu()
    },
    // 进度条变化，更换试图
    onProgressChange(progress) {
      const percentage = progress / 100
      const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
      this.rendition.display(location)
    },
    // 注册主题
    registerTheme () {
      this.themeList.forEach(theme => {
        // console.log('123')
        this.themes.register(theme.name, theme.style)
      })
    },
    // 切换主题
    setTheme (index) {
      this.themes.select(this.themeList[index].name)
      this.defaultStyle = index
    },
    // 翻到前一页
    prevPage() {
      if (this.rendition) {
        this.rendition.prev().then(() => {
          var currentLocation = this.rendition.currentLocation()
          this.progress = Math.floor(((this.locations.percentageFromCfi(currentLocation.start.cfi)).toFixed(5)) * 10000) / 100
          this.$refs.menubar.onProgressInput(this.progress)
        })
      }
    },
    // 翻到下一页
    nextPage() {
      if (this.rendition) {
        this.rendition.next().then(() => {
          var currentLocation = this.rendition.currentLocation()
          this.progress = Math.floor(((this.locations.percentageFromCfi(currentLocation.start.cfi)).toFixed(5)) * 10000) / 100
          this.$refs.menubar.onProgressInput(this.progress)
        })
      }
    },
    titleAndMenu() {
      this.isshow = !this.isshow
      if (!this.isshow) {
        this.$refs.menubar.hideSetting()
      }
    },
    showEpub() {
      this.book = new Epub(DOWNLOAD_URL)
      this.rendition = this.book.renderTo('read', {
        width: window.innerWidth,
        height: window.innerHeight
      })
      // 通过rendition.display渲染电子书
      this.rendition.display()
      // 获取themes对象
      this.themes = this.rendition.themes
      // 默认调用setFoneSize 改变默认字体大小
      this.setFontSize(this.defaultFontSize)
      // this.themes.register(name,style) 注册主题
      // this.themes.select(name) 选择主题
      this.registerTheme()
      this.setTheme(this.defaultStyle)
      // 获取locations 对象
      // 通过epubjs的钩子函数来实现
      this.book.ready.then(() => {
        // 定义一个navigation对象
        this.navigation = this.book.navigation
        console.log(this.navigation)
        return this.book.locations.generate()
      }).then(result => {
        this.locations = this.book.locations
        console.log(this.locations)
        this.bookAvailable = true
      })
    },
    setFontSize (fontSize) {
      this.defaultFontSize = fontSize
      if (this.themes) {
        // 因为fontSize是数值型，要让他生效则要加上px
        this.themes.fontSize(fontSize + 'px')
      }
    }
  },
  mounted() {
    this.showEpub()
  }
}
</script>

<style lang='scss'>
@import './assets/style/global';
.ebook{
  position: relative;
}
  .read-wrapper{
    .mask{
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 100;
      display: flex;
      // background: yellow;
      .left{
        flex: 0 0 px2rem(100);
      }
      .center{
        flex: 1;
      }
      .right{
        flex: 0 0 px2rem(100);
      }
    }
  }
</style>
