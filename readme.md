# 手把手在taro3使用vue3
本文目录如下：
- Vue3新特性介绍：Composition API、Teleport、Fragments、语法糖
- taro3如何安装
- taro3 demo使用Vue3
- taro3如何编译
- 结论
- 推荐文章和参考文章

##  Vue3新特性
Vue3发布后，比较显著的新特性包括以下4点：
### Composition API[<sup>1</sup>](#refer-anchor-1)
Vue2.X采用的是基于Option API（选项API）构建组件，一般来说组件拥有data、methods、computed等选项。这是一种属性相互分隔开的模式，好处是各属性内容分离开，对于新手来说比较友好但对于大型项目来说，为了修改某个功能，可能需要在一个文件中反复上下切换。Vue3增加了Composition API方式（组合API），基于reactivity（响应式）来进行组件构建，把逻辑封装到函数中，可以实现类似React hooks的逻辑组合和重用。来一张官网对比图可以更直观的感受。对于大型项目，每个功能集合在一块，有哪些参数、方法，可以更便捷的了解修改。
![img](https://user-images.githubusercontent.com/499550/62783026-810e6180-ba89-11e9-8774-e7771c8095d6.png)
### Teleport（传入）[<sup>2</sup>](#refer-anchor-2)
类似页面片功能，将一些全屏模式组件（例如Toast）移到Vue app节点外，使得我们在UI界面上不需要修改其他组件样式，就可实现全屏蒙层、浮层弹窗等效果。
### Fragments（碎片）
Vue2.x版本中不支持多根组件，Vue3支持，这点比较好理解，下述组件设计在Vue3中是OK的
```
<!-- Layout.vue -->
<template>
  <header>...</header>
  <main v-bind="$attrs">...</main>
  <footer>...</footer>
</template>
```
### 实验性质的语法糖`<script setup>、<style vars>`
1. `<script setup>`:用于在 SFC 中使用 Composition API 的语法糖，改善在单个文件组件中使用Composition API时的体验。[<sup>3</sup>](#refer-anchor-3)

来自官网一个简单的示例：
```
<script setup="props, { emit }">
import { watchEffect } from 'vue'

watchEffect(() => console.log(props.msg))
emit('foo')
</script>
```
上述代码将被编译为：
```
import { watchEffect } from 'vue'

// setup is exported as a named export so it can be imported and tested
export function setup(props, { emit }) {
  watchEffect(() => console.log(props.msg))
  emit('foo')
  return {}
}

export default {
  setup,
}
```
`<script setup>`语法糖多数情况下可以和TS配合使用，同样官网的示例：
```
<script setup="props" lang="ts">
import { computed } from 'vue'

// declare props using TypeScript syntax
// this will be auto compiled into runtime equivalent!
declare const props: {
  msg: string
}

export const computedMsg = computed(() => props.msg + '!!!')
</script>
```
上述代码将被编译为：
```
<script lang="ts">
import { computed, defineComponent } from 'vue'

export default defineComponent({
  props: ({
    msg: String
  } as unknown) as undefined,
  setup(props: {
    msg: string
  }) {
    const computedMsg = computed(() => props.msg + '!!!')

    return {
      computedMsg,
    }
  }
})
</script>
```


2. `<style vars>`: SFC 中状态驱动的 CSS 变量[<sup>4</sup>](#refer-anchor-4)


它提供了直接的CSS配置和封装，支持将组件状态驱动的CSS变量注入到“单个文件组件”样式中。

该功能输出样式时是静态文件，也就是说，我们不能在组件状态中对改变他。
来自官网示例如下，我们看到文字颜色是红色：
```
<template>
  <div class="text">hello</div>
</template>

<script>
export default {
  data() {
    return {
      color: 'red'
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

虽然在组件内部不能修改，但是我们可以在父组件中修改属性，从而实现更新。比如下述代码：
```
<!-- index.vue -->
<template>
  <view class="index">
    <Styledemo :color = color @click="changeColor"/>
  </view>
</template>

<script>
import { ref } from 'vue'

import Styledemo from "@/floors/styledemo"
import './index.scss'

export default {
  components:{
    Styledemo,
  },
  setup () {
    const color = ref('blue')
    const changeColor = ()=>{
      color.value = 'red'
    }

    return {
      changeColor,
      color,
    }
  },
  onReady () {
    console.log('onReady')
  }
}
</script>

```
```
<!-- styledemo.vue -->
<template>
  <div class="text">文字颜色为{{ color }},点击后变为红色</div>
</template>

<script>
import { ref,toRefs } from 'vue'
export default {
  props:{
    color: {type: String}
  },
   data(props) {
    return {
      color: ref(props)//'red'
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


### 另外Vue3还提供了自定义渲染，在文末提供了推荐阅读文章。


既然Vue3有这么多新特性来帮助我们更加优雅的开发，在taro3上面，是否能够正常使用这些特性，是否需要额外的转换呢？我们来试试看吧~
##  taro3如何安装[<sup>5</sup>](#refer-anchor-5)
*安装taro3*
```
# 使用 npm 安装 CLI
$ npm install -g @tarojs/cli
# OR 使用 yarn 安装 CLI
$ yarn global add @tarojs/cli
# OR 安装了 cnpm，使用 cnpm 安装 CLI
$ cnpm install -g @tarojs/cli
```
*项目初始化*
```
$ taro init
```
![安装](https://img11.360buyimg.com/imagetools/jfs/t1/122690/30/13412/41842/5f6c47b4E27bb42e4/84d6984f617f40d5.jpg)

*安装依赖*
```
# 使用 yarn 安装依赖
$ yarn
# OR 使用 cnpm 安装依赖
$ cnpm install
# OR 使用 npm 安装依赖
$ npm install
```
安装后目录结构：
![taro3目录结构](https://img11.360buyimg.com/imagetools/jfs/t1/112794/33/18575/83251/5f6c47fcE9568cf2b/7c788586432d08dd.jpg)
首页默认代码如下，我们注意到，taro3在新建Vue3项目时已经修改了入口组件写法。
*首页*
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
  },
  onReady () {
    console.log('onReady')
  }
}
</script>
```

*编译运行h5页面*
```
npm run dev:h5
```
编译后首页内容：

![首页](https://img11.360buyimg.com/imagetools/jfs/t1/148409/34/9169/5767/5f6c49a4Ed96e5c53/3c367fcb592b95b0.jpg)

##  taro3是否支持Vue3？
*Composition API*

我们来写个比较简单的todolist计数组件，假设目前已有4项代办事项，点击后将新增一项。这里会使用到compition API思路，以及computed响应式属性。
在用户点击时，第二行“当前操作新增”会根据点击次数递增，共有条数会在前面基础上增加4计算。
```
<template>
  <button @click="increment">
    <p>当前todolist事项已有：{{ existCount }}条；</p>
    <p>当前操作已新增：{{ count }} ，共有{{ total }}条。</p>
  </button>
</template>

<script>
import { ref, computed, onMounted, toRefs, watch } from 'vue'

export default {
  name: 'case1',
  setup(props) {
    const count = ref(0)
    const existCount = ref(4)
    const total = computed(() => count.value + existCount.value )

    function increment() {
      count.value++
    }
    
    onMounted(() => console.log('component mounted!'))
    
    return {
      count,
      increment,
      existCount,
      total,
    }
  }
}
</script>

```
页面表现如下所示：
![todo计数](https://img14.360buyimg.com/imagetools/jfs/t1/122176/18/13461/1349845/5f6c5d14Eaffd062c/f36c7a7279f8aac6.gif)

*Teleport*

我们来写个初次登陆用户的欢迎弹窗。用户名从index.vue传入。index.html页面代码如下：
```
<body>
  <div id="app"></div>
  <!-- teleport页面id，和teleport组件to的名字对应 -->
  <div id="teleportToast"></div>
</body>
```
在Toast.vue中，我们会写个属性to为teleportToast的Teleport组件，代码如下：
```
<template>
  <button @click="showToast" class="btn">打开打开弹窗</button>  
  <!-- to和index.html中的div id一致 -->
  <teleport to="#teleportToast">
    <div v-if="toastFlag" class="toast__wrap"  @click="hideToast">
      <h2>弹窗标题：</h2>
      <div class="toast__wrap--msg">欢迎{{ user }}，点击关闭</div>
    </div>
  </teleport>
</template>
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
  h2{
    margin: 20px;
  }
  &--msg{
    text-align: center;
  }
}
</style>
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
```
以上代码实现了弹窗展示以及关闭功能，实现效果如下：
![Teleport](https://img10.360buyimg.com/imagetools/jfs/t1/111884/14/18645/2307115/5f6c6e91E963e9fcc/0b7230301e9d2e0a.gif)

##  taro3如何编译
以上示例的demo，在taro3中，不需要额外任何操作，即可编译成h5页面。使用Taro3是可以零成本接入Vue3的。具体编译如下：

```
npm run build:h5
```
编译后的代码在“dist”目录下，如下图：

![编译](https://img14.360buyimg.com/imagetools/jfs/t1/130580/10/10663/65179/5f6c6ffcEdda97e88/2359dcf9c4603c4c.jpg)

##  结论：在taro3中可随意使用Vue3
我们将上诉几个demo的组件整合在项目中，访问以下地址：

[demo](https://lillian98.github.io/vue3/dist/#/pages/index/index)

在taro3中使用到的Vue3新特性都能正常表现，没有额外转换工作。

推荐文章：
- [Vue3和React hooks对比](https://zhuanlan.zhihu.com/p/133819602)
- [SWR](https://github.com/vercel/swr)
- [自定义渲染器的应用](http://hcysun.me/vue-design/zh/renderer-advanced.html#自定义渲染器的应用)

参考文章：
- [1][Compsition API](https://v3.cn.vuejs.org/guide/composition-api-introduction.html)
- [2][Teleport](https://v3.cn.vuejs.org/guide/teleport.html#%E4%B8%8E-vue-components-与-vue-components-一起使用)
- [3][`<script setup>`](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-script-setup.md)
- [4][`<style vars>`](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-style-variables.md)
- [5][taro3安装及使用](https://taro-docs.jd.com/taro/docs/GETTING-STARTED)


