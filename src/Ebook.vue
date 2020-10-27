<template>
  <div class="ebook">
    <!-- 顶部菜单 -->
    <top-menu :ifTitleAndMenu='ifTitleAndMenu' /> 
    <!-- #read 放置电子书 -->
    <div class="read-wrapper">
      <div id="read"></div>
      <!-- .mask 遮罩  -->
      <div class="mask">
        <div class="left" @click='prevPage'></div>
        <div class="center" @click='toggleTitleAndMenu'></div>
        <div class="right" @click='nextPage'></div>
      </div>
    </div>
    <!-- 底部菜单 -->
    <footer-menu
      :fontSizeList="fontSizeList" 
      :ifTitleAndMenu='ifTitleAndMenu' 
      :defaultFontSize='defaultFontSize'
      @setFontSize='setFontSize'
      :themeList='themeList'
      :defaultTheme='defaultTheme'
      @setTheme='setTheme'
      @onProgressChange='onProgressChange'
      :bookAvailable='bookAvailable'
      ref="footerMenu" 
      @jumpTo='jumpTo'
      :navigation='navigation'
    />
  </div>
</template>
<script>
import TopMenu from '@/components/TopMenu'
import FooterMenu from '@/components/FooterMenu'
import Epub from 'epubjs'
global.ePub = Epub
const DOWNLOAD_URL = '/2018_Book_AgileProcessesInSoftwareEngine.epub'
export default {
  data() {
    return {
      ifTitleAndMenu:false,
      fontSizeList:[
        { fontSize: 12 },
        { fontSize: 14 },
        { fontSize: 16 },
        { fontSize: 18 },
        { fontSize: 20 },
        { fontSize: 22 },
        { fontSize: 24 }
      ],
      defaultFontSize:16,
      // 主题颜色
      themeList:[
        {
          name:'default',
          style:{
            body:{
              color:"#000", background:"#fff"
            }
          }
        },
        {
          name:'eye',
          style:{
            body:{
              color:"#000", background:"#ceebba"
            }
          }
        },
        {
          name:'night',
          style:{
            body:{
              color:"#fff", background:"#000"
            }
          }
        },
        {
          name:'golad',
          style:{
            body:{
              color:"#000", background:"rgb(241,236,226)"
            }
          }
        }
      ],
      defaultTheme:0,
      // 图书是否处于可用状态
      bookAvailable:false,
      navigation:{}
    }
  },
  components:{
    TopMenu,
    FooterMenu
  },
  methods:{
      // 电子书得解析和渲染   ipconfig
      showEpub() {
        // 1. 生成Book对象
        this.book = new Epub(DOWNLOAD_URL)
          // console.log(this.book);
        // 2. 生成Rendition 通过Book.renderTo
        this.rendition = this.book.renderTo('read',{
          width:window.innerWidth,
          height:window.innerHeight
        })
        // 3. 通过Rendition.display 渲染电子书
        this.rendition.display()
        // 获取 Theme对象s
        this.themes = this.rendition.themes
        // 设置默认字体大小
        this.setFontSize(this.defaultFontSize)
        // 主题注册
        // this.themes.register(name,style)
        this.registerTheme()
        // 切换主题
        // this.themes.select('default')
        this.setTheme(this.defaultTheme)
        // 获取locations对象 进度条使用
        // navigation对象 目录使用
        // 通过epubjs得钩子函数来实现
        this.book.ready.then(()=>{
          this.navigation = this.book.navigation
          return this.book.locations.generate()
        }).then(res=>{
          // console.log(res);
          this.locations = this.book.locations
          // this.onProgressChange(20)
          this.bookAvailable = true
        })
      },
      // 注册主题
      registerTheme() {
        this.themeList.forEach(theme=>{
          this.themes.register(theme.name,theme.style)
        })
      },
      // 设置主题
      setTheme(index){
        this.themes.select(this.themeList[index].name)
        // console.log(index);
        this.defaultTheme = index
      },
      // 上一页  调用rendition.prev()
      prevPage() {
        if(this.rendition){
          this.rendition.prev()
        }
      },
      // 下一页  调用rendition.next()
      nextPage() {
        if(this.rendition){
          this.rendition.next()
        }
      },
      // 切换顶部和底部菜单显示隐藏
      toggleTitleAndMenu() {
        this.ifTitleAndMenu = !this.ifTitleAndMenu
        // $refs 获取子组件调用方法 隐藏设置字体大小控件
        if(!this.ifTitleAndMenu){
           this.$refs.footerMenu.toggleSettingControl()
        }
      },
      // 子组件自定义事件改变默认字体大小
      setFontSize(fontSize) {
        this.defaultFontSize = fontSize
        // console.log(fontSize);
        // 调用themes得fontSize方法设置字体大小
        if(this.themes){
          this.themes.fontSize(fontSize + 'px')
        }
      },
      // 跳转到指定页数
      onProgressChange(progress){
        const percentage = progress / 100;
        const locations = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0;
        this.rendition.display(locations)
      },
      // 根据连接(目录)跳转到指定目录
      jumpTo(href){ 
        this.rendition.display(href)
        // 跳转完成之后 隐藏菜单
        this.toggleTitleAndMenu()
        // 隐藏菜单弹出设置栏
        this.$refs.footerMenu.toggleSettingControl()
        // 隐藏目录
        this.$refs.footerMenu.hideDirectory()
      }
  },
  mounted() {
      this.showEpub()
  }
}
</script>
<style scoped lang='scss'>
@import '@/assets/style/global.scss';
.ebook{
  position: relative;

  .read-wrapper{
    .mask{
      position: absolute;
      top: 0;
      left: 0;
      z-index: 100;
      width: 100%;
      height: 100%;
      display: flex;
      .left,.right{
        flex: 0 0 px2rem(100);
      }
      .center{
        flex: 1;
        cursor: pointer;
      }
    }
  }

}

</style>