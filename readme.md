title: 使用 Taro3 开发小程序之 Vue3 初体验
subtitle: Taro3 使用 Vue3 是什么样的体验呢？让我们带你探索一番吧
cover: https://img13.360buyimg.com/imagetools/jfs/t1/124018/6/13423/280481/5f6d5950E70fd8329/5e5eca596af36836.jpg
category: 经验分享
tags: 
    - Vue3
    - Taro3
    - Taro
    - 小程序
author:
    nick: lillian
date: 2020-09-25 09:00:00
---

## 前言

9 月 19 日凌晨，Vue3 在经过多个开发版本的迭代后，终于迎来了它的正式版本，"One Piece" 的代号也昭示了其开拓伟大航路的野心。

Vue3 的内容相信大家已经都有所了解，对于小程序开发而言，我们是否也可以在写小程序的时候使用 Vue3 呢？我们在 Taro 的文档里找到了[答案](https://taro-docs.jd.com/taro/docs/vue3)，一如我们所期待的，Taro 不仅可以支持使用 React 开发也支持使用 Vue 开发。但它对 Vue3 的支持如何呢？我们今天带大家一探究竟。

##  我们先回顾 Vue3 新特性

Vue3 发布后，比较显著的新特性包括以下 4 点：

### 1.Composition API

Vue2.X 采用的是基于 Option API（选项API）构建组件，一般来说组件拥有 data、methods、computed 等选项。这是一种属性相互分隔开的模式，好处是各属性内容分离开，对于新手来说比较友好但对于大型项目来说，为了修改某个功能，可能需要在一个文件中反复上下切换。Vue3 增加了 Composition API 方式（组合 API ），基于 reactivity（响应式）来进行组件构建，把逻辑封装到函数中，可以实现类似React hooks 的逻辑组合和重用。来一张官网对比图可以更直观的感受。对于大型项目，每个功能集合在一块，有哪些参数、方法，可以更便捷的了解修改。

### 2.Teleport（传入）

类似页面片功能，将一些全屏模式组件（例如Toast）移到Vue app节点外，使得我们在UI界面上不需要修改其他组件样式，就可实现全屏蒙层、浮层弹窗等效果。

### 3.Fragments（碎片）

Vue2.x版本中不支持多根组件，Vue3支持，这点比较好理解，下述组件设计在Vue3中是OK的

```
<!-- Layout.vue -->
<template>
  <header>...</header>
  <main v-bind="$attrs">...</main>
  <footer>...</footer>
</template>
```

### 4.实验性质的语法糖 `<script setup>、<style vars>`

a. `<script setup>`:用于在 SFC 中使用 Composition API 的语法糖，改善在单个文件组件中使用Composition API时的体验。

b. `<style vars>`: SFC 中状态驱动的 CSS 变量，它提供了直接的CSS配置和封装，支持将组件状态驱动的CSS变量注入到“单个文件组件”样式中。

除了以上 4 点之外，Vue3 还提供了自定义渲染，详细可以参考文末的推荐阅读文章。

## 我们如何在 Taro 里使用 Vue3 呢

Vue3 带来的新特性可以让我们更加优雅和高效地进行开发，现在，怀着期待开启我们在 Taro 里使用 Vue3 的体验之旅吧。

Taro 已经默认安装 Taro3 ，所以正常安装即可

``` bash
# 使用 npm 安装 CLI
$ npm install -g @tarojs/cli
# OR 使用 yarn 安装 CLI
$ yarn global add @tarojs/cli
# OR 安装了 cnpm，使用 cnpm 安装 CLI
$ cnpm install -g @tarojs/cli
```

安装完成之后，使用 `taro --version` 查看一下是否安装成功，如果输出版本号说明安装成功。

安装成功后，我们初始化一个项目

``` bash
$ taro init
```

将出现如下图的一些选型

![安装](https://img11.360buyimg.com/imagetools/jfs/t1/122690/30/13412/41842/5f6c47b4E27bb42e4/84d6984f617f40d5.jpg)

如上图：

1. 请输入项目名称？输入项目名称 `vuedemo`
2. 请输入项目介绍？输入项目介绍 `a demo project`
3. 请选择框架？当然是 `Vue3` 啦
4. 如上面 3 个选项，根据自己的需要选择就好，不会有什么问题的

一般情况下，依照提示选型好以后， Taro 是会帮助我们自动安装好依赖的，但有时会因为网络的问题，自动安装没有成功，这时我们就需要手动安装一下了。

```
# 使用 yarn 安装依赖
$ yarn
# OR 使用 cnpm 安装依赖
$ cnpm install
# OR 使用 npm 安装依赖
$ npm install
```

安装后目录结构：

![Taro3目录结构](https://img11.360buyimg.com/imagetools/jfs/t1/112794/33/18575/83251/5f6c47fcE9568cf2b/7c788586432d08dd.jpg)

`app` 默认代码如下，我们注意到，Taro3 在新建 Vue3 项目时已经修改了入口组件写法。

```js
import { createApp } from 'vue'
import './app.scss'

const App = createApp({
  onShow (options) {},
  // 入口组件不需要实现 render 方法，即使实现了也会被 taro 所覆盖
})

export default App
```

`page/index` 目录下 `index` 文件示例如下：

```
<template>
  <view class="index">
    <text>{{ msg }}</text>
  </view>
</template>

<script>
import { ref } from 'vue'
import './index.scss'

export default {
  setup () {
    const msg = ref('Hello world')
    return {
      msg
    }
  }
}
</script>
```

编译运行微信小程序

```
npm run dev:weapp
```

编译后，我们打开微信开发者工具导入编译后的 `dist` 目录，首页内容和编译成 H5 都如下

![首页](https://img11.360buyimg.com/imagetools/jfs/t1/148409/34/9169/5767/5f6c49a4Ed96e5c53/3c367fcb592b95b0.jpg)

##  Taro3 支持 Vue3 到什么程度呢？

### Composition API

我们来写个比较简单的 todolist 计数组件，假设目前已有 4 项代办事项，点击后将新增一项。这里会使用到compition API 思路，以及 computed 响应式属性。

在用户点击时，第二行“当前操作新增”会根据点击次数递增，共有条数会在前面基础上增加 4 计算。

``` html
<template>
  <button @tap="increment">
    增加 1
  </button>
  <view>当前todolist事项已有：{{ existCount }}条；</view>
  <view>当前操作已新增：{{ count }} ，共有{{ total }}条。</view>
</template>

<script>
import { ref, computed, onMounted, toRefs, watch } from 'vue'

export default {
  name: 'case1',
  setup(props) {
    // ref响应式变量
    const count = ref(0)
    const existCount = ref(4)
    // computed方法，在count的value发生改变时，会触发计算total
    const total = computed(() => count.value + existCount.value )

    function increment() {
      count.value++
    }
    
    onMounted(() => console.log('component mounted!'))
    
    return {
      // 返回increment方法，existCount、count、total属性，供模板中调用
      increment,
      existCount,
      count,
      total,
    }
  }
}
</script>

```

页面表现如下所示，可见支持的还是不错的。

![todo计数](https://img14.360buyimg.com/imagetools/jfs/t1/122176/18/13461/1349845/5f6c5d14Eaffd062c/f36c7a7279f8aac6.gif)

### Teleport

我们来写个初次登陆用户的欢迎弹窗。用户名从 `index.vue` 传入。首页代码如下：

``` html
<template>
  <view class="index">
    <Toast :user = username />
    <view id="teleportToast"></view>
  </view>
</template>
```

在 Toast.vue 中，我们会写个属性 to 为 teleportToast 的 Teleport 组件，代码如下：

``` html
<template>
  <button @tap="showToast" class="btn">打开弹窗</button>  
  <!-- to和index.html中的view id一致 -->
  <teleport to="#teleportToast">
    <view v-if="toastFlag" class="toast__wrap"  @tap="hideToast">
      <view class="h2">弹窗标题：</view>
      <view class="toast__wrap--msg">欢迎{{ user }}，点击关闭</view>
    </view>
  </teleport>
</template>

<script>
import { ref,toRefs } from 'vue';
export default {
  props:{
    user: {type: String}
  },
  setup(props) {
    // 获取用户名
    const { user } = toRefs(props)
    // Toast：显示flag
    const toastFlag = ref(false)
    let timer
    const showToast = ()=>{
      toastFlag.value = true
    }
    const hideToast = ()=>{
      toastFlag.value = false
    }
    return {
      user,
      toastFlag,
      showToast,
      hideToast,
    }
  }
}
</script>
<style lang="scss">
.toast__wrap{
  position: fixed;
  width:100%;
  height: 100%;
  background: rgba(0,0,0,.8);
  top: 0;
  left: 0;
  z-index: 101;
  color: #fff;
  .h2{
    margin: 20px;
  }
  &--msg{
    text-align: center;
  }
}
</style>
```

`Teleport` 在小程序下报错了，在 H5 下是正常显示的，弹窗展示以及关闭功能效果如下：

![Teleport](https://img10.360buyimg.com/imagetools/jfs/t1/111884/14/18645/2307115/5f6c6e91E963e9fcc/0b7230301e9d2e0a.gif)

### Fragments

这个我们已经在上面的例子里运用了，完全没问题。

### script setup 语法糖
我们尝试一下 `<style setup>` ，组件里的代码如下：
``` html
<script setup=" props ">
  import { ref, toRefs } from 'vue'
  export default{
    props: {
      msg: String,
    },
  }
  export const count = ref(0)
  export const info = ref(props.msg)
  export const incAndChangeInfo = () => {
    count.value++
    info.value = "change hello" + count.value
  }
</script>
```
上述代码效果等同于下面：
``` html
<script>
import { ref, toRefs } from 'vue'

export default {
  props: {
    msg: String,
    },
  setup(props) {
  const count = ref(0)
  const info = ref(props.msg)
  const incAndChangeInfo = () => {
    count.value++
    info.value = "change hello" + count.value
    }

  return {
    count,
    info,
    incAndChangeInfo,
    }
  }
}
</script>
```
调用它的代码传入mgs如下：
``` html
<Setup msg="hello"/>
```
页面整体表现如下：

![script-setup](https://img10.360buyimg.com/imagetools/jfs/t1/132702/4/11153/1478203/5f705361E4db3e0cc/d32765423246de84.gif)

对比一下，会发现语法糖的确书写更便捷。


### style vars语法糖

我们尝试一下 `<style vars>` ，组件里的代码如下：
``` html
<template>
  <view class="text">文字颜色为{{ color }},点击后变为红色</view>
</template>

<script>
import { ref,toRefs } from 'vue'
export default {
  props:{
    color: {type: String}
  },
   data(props) {
    return {
      color: ref(props) //'red'
    }
  },
  setup(props){
    const { color } = toRefs(props)
    return {
      color,
    }
  }
}
</script>

<style vars="{ color }">
.text {
  color: var(--color);
}
</style>
```

调用它的页面里的代码如下：

```html
<template>
  <view class="index">
    <Styledemo :color = color @tap="changeColor"/>
  </view>
</template>

<script>
import { ref, computed, onMounted, toRefs, watch } from 'vue'
import Styledemo from "@/floors/styledemo"

export default {
  components:{
    Styledemo
  },
  setup () {

    const color = ref('blue')
    const changeColor = ()=>{
      color.value = 'red'
    }

    return {
      changeColor,
      color
    }
  }
}
</script>
```

小程序和 H5 都没有问题，功能效果如下：
![style-vars](https://img11.360buyimg.com/imagetools/jfs/t1/146777/39/9447/1836911/5f702d5eE52e470f9/dea5082ff993a478.gif)




##  结语

我们将上述的几个 demo 的组件整合在一个项目中，访问以下地址：

代码：[demo github](https://github.com/lillian98/vue3)

在线预览：[demo](https://lillian98.github.io/vue3/dist/#/pages/index/index)

经过简单的实践，我们发现，在 Taro3 中使用 Vue3 写小程序是可以的，兼容性还不错。如果你使用 Vue 做小程序开发且想使用 Vue3，使用 Taro3 是个不错的选择，可以享受 Vue3 带来的便利，节约开发时间。不知道别的小程序框架什么时候支持 Vue3，Taro3 在技术上倒是一直保持进取，Taro2 就已经支持 React hooks，Vue3 的支持从 [Taro RFCS](https://github.com/NervJS/taro-rfcs/blob/master/rfcs/0001-vue-3-support.md) 的日期 `2020-06-03` 看来也算很早就计划支持，确实比较难得，毕竟他们正式版是 7 月才刚刚发布。

推荐文章：
- [Vue3和React hooks对比](https://zhuanlan.zhihu.com/p/133819602)
- [SWR](https://github.com/vercel/swr)
- [自定义渲染器的应用](http://hcysun.me/vue-design/zh/renderer-advanced.html#自定义渲染器的应用)

参考文章：
- [1][Compsition API](https://v3.cn.vuejs.org/guide/composition-api-introduction.html)
- [2][Teleport](https://v3.cn.vuejs.org/guide/teleport.html#%E4%B8%8E-vue-components-与-vue-components-一起使用)
- [3][`<script setup>`](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-script-setup.md)
- [4][`<style vars>`](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-style-variables.md)
- [5][Taro3安装及使用](https://taro-docs.jd.com/taro/docs/GETTING-STARTED)


