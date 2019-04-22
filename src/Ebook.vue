<template>
    <div class="ebook">
        <title-bar :showing="showing"></title-bar>
        <div class="read-wrapper">
            <div id="read"></div>
            <div class="mask">
                <div class="left" @click="prevPage"></div>
                <div class="center" @click="showMenu"></div>
                <div class="right" @click="nextPage"></div>
            </div>
        </div>
        <menu-bar ref="menuBar" 
                  :fontSizeList="fontSizeList" 
                  :showing="showing"
                  :defaultFontSize = "defaultFontSize"
                  :themeList = "themeList"
                  :defaultThemes = "defaultThemes"
                  :bookAvailable = "bookAvailable"
                  :navigation="navigation"
                  @setSize = "setFontSize"
                  @selThemes = "selThemes"
                  @onProgressChange = "onProgressChange"
                  @jumpTo = "jumpTo"

        >
        </menu-bar>
    </div>
</template>

<script>
import Epub from "epubjs";
import titleBar from "@/components/titleBar";
import menuBar from "@/components/menuBar"
const DOWNLOAD_URL =
  "/static/epub/2018_Book_AgileProcessesInSoftwareEngine.epub";
export default {
  data() {
    return {
      showing: false,
      fontSizeList:[
        {fontSize:'12'},
        {fontSize:'14'},
        {fontSize:'16'},
        {fontSize:'18'},
        {fontSize:'20'},
        {fontSize:'22'}
      ],
      defaultFontSize:"14",
      themeList:[
        {
          name:"default",
          style:{
            body:{
              'color':'#000',
              'background':'#fff'
            }
          }
        },
        {
          name:"eye",
          style:{
            body:{
              'color':'#000',
              'background':'#ceeaba'
            }
          }
        },
        {
          name:"night",
          style:{
            body:{
              'color':'#fff',
              'background':'#000'
            }
          }
        },
        {
          name:"gold",
          style:{
            body:{
              'color':'#000',
              'background':'rgba(241,236,226)'
            }
          }
        }
      ],
      defaultThemes:0,
      bookAvailable:false,
      navigation:null
    };
  },
  components:{
    titleBar,
    menuBar
  },
  methods: {
    showMenu() {
      this.showing = !this.showing;
      this.$refs.menuBar.hideFonSet() 
    },
    prevPage() {
      this.rendition.prev();
    },
    nextPage() {
      this.rendition.next();
    },
    showEpub() {
      //新建Epub实例
      this.book = new Epub(DOWNLOAD_URL);
      //通过renderTo将图书挂载到指定节点
      this.rendition = this.book.renderTo("read", {
        width: window.innerWidth,
        height: window.innerHeight
      });
      //通过rendition.display渲染电子书
      this.rendition.display();
      //获取themes对象
      this.themes = this.rendition.themes
      //设置默认字体
      this.setFontSize(this.defaultFontSize)
      //注册主题
      this.setRegister()
      //设置默认主题
      this.selThemes(this.defaultThemes)
      //获取location对象
      //必须通过epubjs的钩子函数来实现
      this.book.ready.then(()=>{
        //获取navigation对象
        this.navigation = this.book.navigation;
        console.log(this.navigation.toc)
        return this.book.locations.generate()
      }).then(result=>{
        this.locations = this.book.locations
        this.bookAvailable = true
      })
    },
    setFontSize(size){
      this.defaultFontSize = size + ""
      if(this.themes) {
        this.themes.fontSize(size + "px")
      }
    },
    setRegister(){
      this.themeList.forEach(themes=>{
        this.themes.register(themes.name,themes.style)
      })
    },
    selThemes(index){
      this.themes.select(this.themeList[index].name)
      this.defaultThemes = index
    },
    onProgressChange(progress) {
      const percentage = progress / 100
      const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
      this.rendition.display(location)
    },
    jumpTo(href){
      this.rendition.display(href);
      this.hideTitleAndMenu()
    },
    hideTitleAndMenu(){
      //隐藏标题栏和菜单栏
       this.showing = false;
       //隐藏菜单栏弹出的设置兰
       this.$refs.menuBar.hideFonSet();
       //隐藏目录
       this.$refs.menuBar.hideContent();
    }
  },
  mounted() {
    this.showEpub();
  }
};
</script>

<style lang="scss">
@import "./assets/styles/global.scss";
.ebook {
  position: relative;
  .read-wrapper {
    .mask {
      position: absolute;
      top: 0;
      left: 0;
      z-index: 100;
      display: flex;
      width: 100%;
      height: 100%;
    }
    .left {
      flex: 0 0 px2rem(100);
    }
    .center {
      flex: 1;
    }
    .right {
      flex: 0 0 px2rem(100);
    }
  }
}
</style>