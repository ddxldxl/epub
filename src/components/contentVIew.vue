<template>
<transition name="slide-in">
    <div class="content">
        <div class="hrefMenu" v-if="bookAvailable">
            <div class="menu" v-for="(item,index) in navigation.toc" :key="index" @click="jumpTo(item.href)">
                <span>{{item.label}}</span>
            </div>
        </div>
        <div class="empty" v-else>加载中...</div>
    </div>
</transition>
</template>
<script>
export default {
    props:{
        ifShowContent:Boolean,
        navigation:Object,
        bookAvailable:Boolean
    },
    methods:{
        jumpTo(href){
            this.$emit("jumpTo",href)
        }
    }
}
</script>
<style lang="scss">
@import "../assets/styles/global.scss";
    .content {
        position: absolute;
        left: 0;
        top:0;
        width: 80%;
        height: 100%;
        background-color: #fff;
        z-index: 102;
        .hrefMenu {
            position: absolute;
            left: 0;
            top: 0;
            height: 100%;
            padding: px2rem(20);
            box-sizing: border-box;
            display: flex;
            flex-direction: column;
            justify-content: flex-start;
            .menu {
                padding: px2rem(10) 0;
                span {
                    font-size: px2rem(14)
                }
            }
        }
        .empty {
            position: absolute;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            padding: px2rem(20);
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: px2rem(22);
            color: #999;
            box-sizing: border-box;
        }
    }
</style>
