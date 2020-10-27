<template>
  <!-- 底部菜单 -->
  <div>
    <transition name="slide-up">
        <div 
          class="footer-menu-wrapper" 
          :class="{'hide-box-shadow': ifSettingControl || !ifTitleAndMenu}" 
          v-show="ifTitleAndMenu"
          >
        <div class="icon-wrapper">
            <div class="iconfont icon-menu" @click='showSetting(3)'></div>
        </div>
        <div class="icon-wrapper">
            <div class="iconfont icon-progress" @click='showSetting(2)'></div>
        </div>
        <div class="icon-wrapper">
            <div class="iconfont icon-bright" @click='showSetting(1)'></div>
        </div>
        <div class="icon-wrapper">
            <div class="iconfont icon-a" 
              @click="showSetting(0)"
              >A</div>
        </div>
        </div>
    </transition>
  <!-- 底部菜单控件 -->
    <transition name="slide-up">
          <!-- 设置字体大写 -->
      <div class="setting-wrapper" v-show="ifSettingControl">
        <div class="setting-font-size" v-if='showTag === 0'>
          <div class="preview"
            :style="{fontSize: fontSizeList[0].fontSize + 'px'}"
          >A</div>
          <div class="select">
            <div class="select-wrapper" 
              v-for="(item,index) in fontSizeList" :key='index'
              @click='setFontSize(item.fontSize)'
            >
              <div class="line"></div>
              <div class="point-wrapper">
                <!-- 当前字体大小与默认字体大小一致 为选中状态 -->
                <div class="point" v-show='defaultFontSize === item.fontSize'>
                  <div class="small-point"></div>
                </div>
              </div>
              <div class="line"></div>
            </div>
          </div>
          <div class="preview"
            :style="{fontSize: fontSizeList[fontSizeList.length - 1].fontSize + 'px'}"
          >A</div>
        </div>
        <!-- 设置主题 -->
        <div class="setting-theme" v-else-if="showTag === 1">
          <div 
            class="setting-theme-item" 
            v-for="(item,index) in themeList" 
            :key='index' 
            @click="setTheme(index)"
          >
            <div class="previw" 
              :style="{background: item.style.body.background}"
              :class="{'no-border': item.style.body.background !== '#fff'}"
              ></div>
            <div class="text" :class="{'selected': index === defaultTheme}">{{item.name}}</div>
          </div>
        </div>
        <!-- 进度条 -->
        <div class="setting-progress" v-else-if="showTag === 2">
          <div class="progress-wrapper">
            <input class="progress" 
                   type="range"
                   max='100'
                   min='0'
                   step='1'
                   @change='onProgressChange($event.target.value)'
                   @input='onProgressInput($event.target.value)'
                   :value='progress'
                   :disabled='!bookAvailable'
                   ref='progress'
            >
          </div>
          <div class="text-wrapper">
            <span>{{bookAvailable ? progress + '%' : '加载中...'}}</span>
          </div>
        </div>
      </div>
    </transition>
    <!-- 目录 -->
    <directory :ifShowContent="ifShowContent"
                  v-show="ifShowContent"
                  :navigation="navigation"
                  :bookAvailable="bookAvailable"
                  @jumpTo="jumpTo">
    </directory>
    <transition name="fade">
      <div class="content-mask"
           v-show="ifShowContent"
           @click='hideDirectory'
      ></div>
    </transition>
  </div>
</template>
<script>
import Directory from './Directory'
export default {
  props: {
    ifTitleAndMenu:{
        type: Boolean,
        default: false
    },
    fontSizeList:{
      type: Array,
      default: []
    },
    defaultFontSize:{
      type:Number,
      default:16
    },
    themeList:{
      type:Array,
    },
    defaultTheme:Number,
    bookAvailable:{
      type:Boolean,
      default:false
    },
    navigation:{
      type:Object
    }
  },
  data() {
    return {
      ifSettingControl: false,
      showTag:0,
      progress:0,
      ifShowContent:false
    }
  },
  components:{
    Directory
  },
  methods: {
    // 显示隐藏设置控件
    showSetting(tag){
      this.ifSettingControl = true
      this.showTag = tag
      if (this.showTag === 3) {
        this.ifSettingShow = false
        this.ifShowContent = true
      } else {
        this.ifSettingShow = true
      }
    },
    // 为组件调用 隐藏设置控件
    toggleSettingControl(){
      this.ifSettingControl = false
    },
    // 点击 修改字体大写
    setFontSize(fontSize){
      this.$emit('setFontSize',fontSize)
    },
    // 自定义事件 改变主题
    setTheme(index){
      this.$emit('setTheme',index);
    },
    // 进度条松开时触发 根据进度条得值跳转到指定位置
    onProgressChange(progress){
      this.$emit('onProgressChange',progress)
    },
    // 推动进度条时触发事件 修改进度条颜色占比
    onProgressInput(progress){
      this.progress = progress
      this.$refs.progress.style.backgroundSize = `${this.progress}% 100%`
    },
    // 隐藏目录
    hideDirectory(){
      this.ifShowContent = false
    },
    // 跳转方法，调用父组件方法
    jumpTo(target) {
      this.$emit('jumpTo', target)
    },
  }

}
</script>
<style scoped lang='scss'>
@import '@/assets/style/global.scss';
.footer-menu-wrapper {
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  height: px2rem(48);
  z-index: 102;
  background: white;
  box-shadow: 0 px2rem(-8) px2rem(8) rgba(0,0,0,.15);
  display: flex;
  &.hide-box-shadow{
    box-shadow: none;
  }
  .icon-wrapper{
    flex: 1;
    @include center
  }
}
.setting-wrapper {
  position: absolute;
  bottom: px2rem(48);
  left: 0;
  width: 100%;
  height: px2rem(60);
    z-index: 101;
  background: white;
  box-shadow: 0 px2rem(-8) px2rem(8) rgba(0,0,0,.15);
  .setting-font-size{
    height: 100%;
    display: flex;
    .preview{
      flex: 0 0 px2rem(40);
      @include center
    }
    .select{
      flex: 1;
      display: flex;
      .select-wrapper{
        flex: 1;
        display: flex;
        align-items: center;
        .line{
          flex: 1;
          height: 0;
          border-top: px2rem(1) solid silver;
        }
        .point-wrapper{
          position: relative;
          flex: 0 0 0;
          width: 0;
          height:px2rem(7);
          border-left: px2rem(1) solid silver;
          cursor: pointer;
          .point{
            position: absolute;
            top: 0;
            left: 0;
            transform: translate(-37%,-37%);
            width: px2rem(18);
            height: px2rem(18);
            background: white;
            border-radius: 50%;
            border: px2rem(1) solid #ccc;
            box-shadow: 0 px2rem(4) px2rem(4) rgba(0,0,0,.15);
            @include center;
            .small-point{
              width: px2rem(5);
              height: px2rem(5);
              border-radius: 50%;
              background: #2c3e50;
            }
          }
        }
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
      }
    }
  }
  .setting-theme{
    height: 100%;
    display: flex;
    .setting-theme-item{
      flex: 1;
      display:flex;
      flex-direction: column;
      padding: px2rem(8);
      box-sizing: border-box;
      .previw{
        flex:1;
        border:px2rem(1) solid #ccc;
        box-sizing: border-box;
        cursor: pointer;
        &.no-border{
          border: none;
        }
      }
      .text{
        flex: 0 0 px2rem(20);
        font-size:px2rem(14);
        color: #ccc;
        @include center;
        &.selected{
          color: #333;
        }
      }
    }
  }
  .setting-progress{
    position: relative;
    width: 100%;
    height: 100%;
    .progress-wrapper{
      width: 100%;
      height: 100%;
      @include center;
      padding: 0 px2rem(30);
      box-sizing: border-box;
      .progress{
        width: 100%;
        -webkit-appearance: none;
        height: px2rem(2);
        background: -webkit-linear-gradient(#999,#999) no-repeat #ddd;
        background-size: 0 100%;
        &:focus{
          outline: none;
        }
        &::-webkit-slider-thumb{
          -webkit-appearance: none;
          height: px2rem(20);
          width: px2rem(20);
          border-radius: 50%;
          background: #fff;
          box-shadow: 0 4px 4px 0 rgba(0,0,0,.15);
          border: px2rem(1) solid #ddd;
        }
      }
    }
    .text-wrapper{
      position: absolute;
      left: 50%;
      bottom: px2rem(10);
      font-size: px2rem(12);
    }
  }
}
.content-mask {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 101;
  display: flex;
  width: 100%;
  height: 100%;
  background: rgba(51,51,51,.8);
  cursor: pointer;
}
</style>