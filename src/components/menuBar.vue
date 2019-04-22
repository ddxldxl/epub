<template>
    <div class="menu-bar">
        <transition name="slide-up">
            <div class="menu-wrapper" v-show="showing" :class="{'hide-box-show':ifMenuShow||!showing}">
                <div class="icon-wrapper">
                    <span class="icon-menu icon" @click="showFonSet(3)"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-progress icon" @click="showFonSet(2)"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-bright icon" @click="showFonSet(1)"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-a icon" @click="showFonSet(0)">A</span>
                </div>
            </div>
        </transition>
        <transition name="slide-up">
            <div class="setting-wrapper" v-show="ifMenuShow">
                <div class="setting-font-size" v-if="tag==0">
                    <div class="preview" :style="{fontSize:fontSizeList[0].fontSize+'px'}">A</div>
                    <div class="select">
                        <div class="select-wrapper" v-for="(item,index) in fontSizeList" :key="index">
                            <div class="line"></div>
                            <div class="point-wrapper"  @click="seFontSize(item.fontSize)">
                                <div class="point" v-if="item.fontSize == defaultFontSize">
                                    <div class="small-point"></div>
                                </div>
                            </div>
                            <div class="line"></div>
                        </div>
                    </div>
                    <div class="preview" :style="{fontSize:fontSizeList[fontSizeList.length-1].fontSize+'px'}">A</div>
                </div>
                <div class="setting-themes" v-else-if="tag==1" >
                    <div class="setting-themes-item" v-for="(item,index) in themeList" :key="index">
                        <div class="preview" 
                            :class="{'no-border':item.style.body.background!='#fff'}" 
                            :style="{background:item.style.body.background}"
                            @click="selThemes(index)"
                        ></div>
                        <div class="text" :class="{selTxt:index==defaultThemes}">{{item.name}}</div>
                    </div>
                </div>
                <div class="setting-progress" v-else-if="tag==2">
                    <div class="progress-wrapper">
                        <input type="range" class="progress" max="100" min="0" step="1"
                            @change="onProgressChange($event.target.value)"
                            @input="onProgressInput($event.target.value)"
                            :value="progress"
                            :disabled = "!bookAvailable"
                            ref="progress"
                        >
                        <div class="text-wrapper">
                            <span>{{bookAvailable?progress+'%':'加载中...'}}</span>
                        </div>
                    </div>
                </div>
            </div>
        </transition>
        <transition name="fade">
            <div class="content-mask" v-show="ifShowContent" @click="hideContent"></div>
        </transition>
        <contentView 
            :ifShowContent="ifShowContent"
            :bookAvailable="bookAvailable"
            v-show="ifShowContent"
            :navigation="navigation"
            @jumpTo="jumpTo"
        ></contentView>
    </div>
</template>
<script>
import contentView from "./contentView"
export default {
  components:{
      contentView
  },
  props: {
    showing: {
      type: Boolean
    },
    fontSizeList:Array,
    defaultFontSize:String,
    themeList:Array,
    defaultThemes:Number,
    bookAvailable:Boolean,
    navigation:null
  },
  data(){
      return {
          ifMenuShow:false,
          tag:0,
          progress:0,
          ifShowContent:false
      }
  },
  methods:{
      showFonSet(tag){
          this.tag = tag
          if(tag==3){
              this.ifMenuShow = false
              this.ifShowContent = true
          }else {
            this.ifMenuShow = true
          }
          
      },
      hideFonSet(){
          this.ifMenuShow = false
      },
      hideContent(){
          this.ifShowContent = false;
      },
      //设置字体大小
      seFontSize(size){
          this.$emit("setSize",size)
      },
      //设置主题
      selThemes(index){
          this.$emit('selThemes',index)
      },
      //拖动进度条出发事件
      onProgressChange(value){
          this.$refs.progress.style.backgroundSize = `${value}% 100%`;
          this.progress = value
      },
      //松开进度条后出发的事件
      onProgressInput(value) {
          this.progress = value
          this.$emit("onProgressChange",value)
      },
      //跳转页面
      jumpTo(href){
          this.$emit("jumpTo",href)
      }
  }
};
</script>
<style lang="scss">
@import "../assets/styles/global.scss";
    .menu-bar {
        .menu-wrapper {
            position: absolute;
            bottom: 0;
            left: 0;
            z-index: 101;
            width: 100%;
            height: px2rem(60);
            background-color: #fff;
            display: flex;
            flex-direction: row;
            justify-content: space-around;
            align-items: center;
            box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, 0.15);
            &.hide-box-show {
                box-shadow: none
            }
            .icon-wrapper {
                @include center;
                .icon-progress {
                font-size: px2rem(28);
                }
                .icon-bright {
                font-size: px2rem(24);
                }
            }
        }
        .setting-wrapper {
            position: absolute;
            bottom: px2rem(48);
            left:0;
            z-index: 101;
            width: 100%;
            height: px2rem(60);
            background: #fff;
            box-shadow: 0 px2rem(-8) px2rem(8) rgba(0,0,0,.15);
            .setting-font-size {
                display: flex;
                height: 100%;
                .preview {
                    flex: 0 0 px2rem(40);
                    @include center
                }
                .select {
                    flex: 1;
                    display: flex;
                    .select-wrapper {
                        flex: 1;
                        display: flex;
                        align-items: center;
                        &:first-child {
                            .line{
                                &:first-child {
                                    border: 0
                                }
                            }
                        }
                        &:last-child {
                            .line{
                                &:last-child {
                                    border: 0
                                }
                            }
                        }
                        .line {
                            flex: 1;
                            height: 0;
                            border-top:px2rem(1) solid #ccc;
                        }
                        .point-wrapper {
                            flex: 0 0 0;
                            width: 0;
                            height: px2rem(7);
                            border-left: px2rem(1) solid #ccc;
                            position: relative;
                            .point {
                                position: absolute;
                                top: px2rem(-8);
                                left:  px2rem(-10);
                                @include center;
                                width: px2rem(20);
                                height: px2rem(20);
                                border-radius: 50%;
                                background: #fff;
                                border: px2rem(1) solid #ccc;
                                box-shadow: 0 px2rem(4) px2rem(4) rgba(0,0,0,.15);
                                .small-point {
                                    width: px2rem(5);
                                    height: px2rem(5);
                                    background-color: #333;
                                    border-radius: 50%;
                                }
                            }
                        }
                    }
                }
            }
        }
        .setting-themes {
            display: flex;
            height: 100%;
            .setting-themes-item {
                flex: 1;
                display: flex;
                flex-direction: column;
                padding: px2rem(5);
                box-sizing: border-box;
                .preview {
                    flex: 1;
                    border: px2rem(1) solid #ccc;
                    &.no-border {
                        border: none
                    }
                }
                .text {
                    flex: 0 0 px2rem(30);
                    font-size: px2rem(16);
                    color: #999;
                    @include center
                }
                .selTxt {
                    color: #333
                }
            }
        }
        .setting-progress {
            position: relative;
            width: 100%;
            height: 100%;
            .progress-wrapper {
                width: 100%;
                height: 100%;
                @include center;
                padding: 0 px2rem(30);
                box-sizing: border-box;
                display: flex;
                flex-direction: column;
                .progress {
                    width: 100%;
                    -webkit-appearance: none;
                    height: px2rem(2);
                    background: -webkit-linear-gradient(#999,#999) no-repeat,#ddd;
                    background-size: 0 100%;
                    &:focus {
                        outline: none;
                    }
                    &::-webkit-slider-thumb {
                        -webkit-appearance: none;
                        width: px2rem(20);
                        height: px2rem(20);
                        border-radius: 50%;
                        background-color: #fff;
                        box-shadow: 0 4px 4px 0 rgba(0,0,0,.15);
                        border:px2rem(1) solid #ddd;
                    }
                }
                .text-wrapper {
                    @include center;
                    font-size: px2rem(14);
                    color: #999;
                    padding: px2rem(5) 0
                }
            }
        }
        .content-mask {
            position: absolute;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            display: flex;
            background-color: rgba(51,51,51,.8);
            z-index: 101;
        }
    }
</style>
