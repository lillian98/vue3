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