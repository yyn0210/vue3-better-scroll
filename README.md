# vue3-better-scroll
基于 Vue3 | TypeScript | BetterScroll 的滑动块移动端页面组件

### Use in SPA

~~~html
<template>
  <scroll-view
    :pull-down-refresh="true"
    :pull-up-load="true"
    :pull-up-load-end="isUpLoadEnd"
    @pullup="onPullupHooks"
    @pulldown="onPulldownHooks"
  >
    <!-- 顶部下拉刷新插糟(提示内容, 当pull-down-refresh为true时展示) -->
    <template v-slot:pull-down-tips>Pull down and load more</template>
    <!-- 顶部下拉刷新插糟(加载内容, 当@pulldown执行中显示) -->
    <template v-slot:pull-down-loading>Loading...</template>
    <!-- 滑动块内容插糟 -->
    <template v-slot:scroll-content>
      <div class="scroll-item" v-for="(item, index) in emojis" :key="index">
        {{ item }}
      </div>
    </template>
    <!-- 底部上拉刷新插糟(提示内容, 当pull-up-load为true时展示) -->
    <template v-slot:pull-up-tips>Pull up and load more</template>
    <!-- 底部上拉刷新插糟(加载内容, 当@pullup执行中显示) -->
    <template v-slot:pull-up-loading>Loading...</template>
    <!-- 底部上拉刷新插糟(结束内容, 当pull-up-load-end为true时展示) -->
    <template v-slot:pull-up-end>End...</template>
  </scroll-view>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'
import ScrollView from '../components/scroll-view.vue'
const emojis = ref([
  '😀 😁 😂 🤣 😃',
  '😄 😅 😆 😉 😊',
  '😫 😴 😌 😛 😜',
  '👆🏻 😒 😓 😔 👇🏻',
  '😑 😶 🙄 😏 😣',
  '😞 😟 😤 😢 😭',
  '................'
])
const isUpLoadEnd = ref(false)

const awaitPromise = (time: number) => {
  return new Promise((resolve) => {
    setTimeout(() => resolve(true), time)
  })
}
// 上拉刷新测试
const onPullupHooks = async (finish: () => void) => {
  await awaitPromise(1000)
  // 更新数据完成后, 调用 finish 刷新状态
  finish()
}
// 上拉刷新测试
const onPulldownHooks = async (finish: () => void) => {
  await awaitPromise(1000)
  // 更新数据完成后, 调用 finish 刷新状态
  finish()
}
</script>
<style lang="scss">
.scroll-item {
  height: 50px;
  line-height: 50px;
  font-size: 24px;
  font-weight: bold;
  border-bottom: 1px solid #eee;
  text-align: center;
  &:nth-child(2n) {
    background-color: #f3f5f7;
  }
  &:nth-child(2n + 1) {
    background-color: #42b983;
  }
}
</style>
~~~

### Attributes:

| 参数                     | 说明                                                         | 类型    |
| ------------------------ | ------------------------------------------------------------ | ------- |
| height                   | 滑动块高度(默认页面高度)                                     | Number  |
| navHeight                | 头部导航高度, 单位 px (默认46)                                | Number  |
| tabHeight                | 底部导航高度, 单位 px (默认50)                                | Number  |
| pullUpLoad               | 是否开启上拉(底部)刷新                                       | Boolean |
| pullUpLoadEnd            | 是否上拉拖动到底了                                           | Boolean |
| pullDownRefresh          | 是否开启下拉(头部)刷新                                       | Boolean |
| pullDownRefreshTopHeight | 下拉加载停止的高度, 单位 px (默认40)                         | Number  |
| options                  | 可自行根据 better-scroll 官方文档 扩展参数 例：`:options="{click:true}"` | Object  |

### Slots:

| name              | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| pull-down-tips    | 顶部下拉刷新提示内容, 当 pullDownRefresh 为 true 时展示, 加载时隐藏 |
| pull-down-loading | 顶部下拉刷新加载内容, 当 pullDownRefresh 为 true 时展示, 加载时线上 |
| scroll-content    | 滚动的主体内容区域                                           |
| pull-up-tips      | 底部上拉刷新提示内容, 当 pullUpLoad 为 true 时展示, 加载与结束时隐藏 |
| pull-up-loading   | 底部上拉刷新加载内容, 当 pullUpLoad 为 true 时展示, 加载时显示, 结束时隐藏 |
| pull-up-end       | 底部上拉刷新提示内容, 当 pullUpLoad 为 true 时展示, 结束时显示 |

### Ref:

| name       | 说明               |
| ---------- | ------------------ |
| ref.scroll | better-scroll 实例 |

### Events:

| 事件名称   | 说明                                                        | 回调参数                                       |
| ---------- | ----------------------------------------------------------- | ---------------------------------------------- |
| @scroll    | 触发时机：滚动过程中，具体时机取决于 options 中的 probeType | pos {Object} 滚动的实时坐标                    |
| @pulldown  | 触发时机：滚动到顶部超出一定距离时，用于下拉刷新            | finish {Function} 重置刷新状态回调（必须调用） |
| @pullingUp | 触发时机：滚动到底部部超出一定距离时，用于上拉刷新          | finish {Function} 重置刷新状态回调（必须调用） |

# Mr.Mao's blog

[ Mr.Mao's blog](https://tuimao233.gitee.io/mao-blog/)
