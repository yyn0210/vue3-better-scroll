<!--
 * @Author: Mr.Mao
 * @LastEditors: Mr.Mao
 * @Date: 2020-12-07 23:50:38
 * @LastEditTime: 2020-12-21 21:29:37
 * @Description: 首页
 * @任何一个傻子都能写出让电脑能懂的代码，而只有好的程序员可以写出让人能看懂的代码
-->
<template>
  <div
    class="scroll-wrapper"
    :style="{ height: props.height - props.navHeight - props.tabHeight + 'px' }"
  >
    <div class="scroll-content">
      <div class="pull-down-slot">
        <slot v-if="isShowPullDownTips" name="pull-down-tips"></slot>
        <slot v-if="isShowPullDownLoad" name="pull-down-loading"></slot>
      </div>
      <slot name="scroll-content"></slot>
      <slot v-if="isShowPullUpTips" name="pull-up-tips"></slot>
      <slot v-if="isShowPullUpload" name="pull-up-loading"></slot>
      <slot v-if="isShowPullUpEnd" name="pull-up-end"></slot>
    </div>
  </div>
</template>

<script setup lang="ts">
/**
 * @evnet {Function} pullingUp(callback) 滚动到底部事件, 用于上拉加载, 需要释放事件
 * @evnet {Function} pullingDown(callback) 滚动到顶部下拉事件, 用于下拉刷新
 */
import { defineEmit, onMounted, ref, defineProps, computed } from 'vue'
import BScroll from '@better-scroll/core'
import PullDown from '@better-scroll/pull-down'
import Pullup from '@better-scroll/pull-up'
// 扩展下拉刷新的能力
BScroll.use(PullDown)
// 扩展上拉加载的能力
BScroll.use(Pullup)

const props = defineProps({
  // 滑动块高度(默认页面高度)
  height: {
    type: Number,
    default: document.documentElement.clientHeight || document.body.clientHeight
  },
  // 头部导航高度(默认46)
  navHeight: {
    type: Number,
    default: 46
  },
  // 底部导航高度(默认50)
  tabHeight: {
    type: Number,
    default: 50
  },
  // 是否开启上拉(底部)刷新
  pullUpLoad: {
    type: Boolean,
    default: false
  },
  // 是否拖动到底了
  pullUpLoadEnd: {
    type: Boolean,
    default: false
  },
  // 是否开启下拉(头部)刷新
  pullDownRefresh: {
    type: Boolean,
    default: false
  },
  // 下拉停止高度
  pullDownRefreshTopHeight: {
    type: Number,
    default: 40
  }
})
const emit = defineEmit()

// 顶部是否处在加载状态
const isPullDownLoad = ref(false)
// 是否展示顶部提示插糟
const isShowPullDownTips = computed(() => {
  return props.pullDownRefresh && !isPullDownLoad.value
})
// 是否展示顶部加载插糟
const isShowPullDownLoad = computed(() => {
  return props.pullDownRefresh && isPullDownLoad.value
})

// 底部是否处在加载状态
const isPullUpload = ref(false)
// 是否展示底部提示插糟
const isShowPullUpTips = computed(() => {
  return props.pullUpLoad && !isPullUpload.value && !props.pullUpLoadEnd
})
// 是否展示底部提示插糟
const isShowPullUpload = computed(() => {
  return props.pullUpLoad && isPullUpload.value && !props.pullUpLoadEnd
})
// 是否展示底部到底插糟
const isShowPullUpEnd = computed(() => {
  return props.pullUpLoad && props.pullUpLoadEnd
})

// 是否展示底部
onMounted(() => {
  // 初始化滚动条
  const scrollEl = document.querySelector<HTMLElement>('.scroll-wrapper')
  if (!scrollEl) {
    return false
  }
  const pullDownRefreshOpts = props.pullDownRefresh && {
    stop: props.pullDownRefreshTopHeight
  }
  const scroll = new BScroll(scrollEl, {
    pullDownRefresh: pullDownRefreshOpts as any,
    pullUpLoad: props.pullUpLoad as any
  })
  // 派发滚动到底部事件, 用于上拉加载
  props.pullUpLoad &&
    scroll.on('pullingUp', () => {
      isPullUpload.value = true
      emit('pullup', () => {
        scroll.finishPullUp()
        isPullUpload.value = false
      })
    })
  // 派发滚动到顶部下拉事件, 用于下拉刷新
  props.pullDownRefresh &&
    scroll.on('pullingDown', () => {
      isPullDownLoad.value = true
      emit('pulldown', () => {
        scroll.finishPullDown()
        isPullDownLoad.value = false
      })
    })
  // 派发滚动事件
  scroll.on('scroll', (pos: { x: number; y: number }) => {
    emit('scroll', pos)
  })
})
</script>
<style lang="scss">
.scroll-wrapper {
  overflow: hidden;
}
.pull-down-slot {
  position: absolute;
  overflow: hidden;
  transform: translateY(-100%) translateZ(0);
}
</style>
