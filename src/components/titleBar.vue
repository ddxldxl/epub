<template>
    <transition name="slide-down">
        <div class="title-wrapper" v-show="showing">
            <div class="left">
                <span class="icon-back icon"></span>
            </div>
            <div class="right">
                <div class="icon-wrapper">
                    <span class="icon-cart icon"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-person icon"></span>
                </div>
                <div class="icon-wrapper">
                    <span class="icon-more icon"></span>
                </div>
            </div>
        </div>
    </transition>
</template>
<script>
export default {
    props:{
        showing:{
            type:Boolean
        }
    }
};
</script>
<style lang="scss">
    @import "../assets/styles/global.scss";
    .title-wrapper {
        position: absolute;
        top: 0;
        left: 0;
        z-index: 101;
        width: 100%;
        height: px2rem(60);
        background-color: #fff;
        display: flex;
        box-shadow: 0 px2rem(8) px2rem(8) rgba(0, 0, 0, 0.15);
        .left {
            width: px2rem(50);
            @include center;
        }
        .right {
            flex: 1;
            display: flex;
            justify-content: flex-end;
            align-items: center;
            .icon-wrapper {
            width: px2rem(40);
            @include center;
            }
        }
    }
</style>