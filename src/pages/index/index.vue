<template>
  <view class="index">
    <!-- <text>{{ msg }}</text> -->
    <h3>
      composition API:
    </h3>
    <Case1 />
    <h3>
      Teleport:
    </h3>
    <Toast :user = username />
    <h3>
      style-var语法糖:
    </h3>
    <Styledemo :color = color @click="changeColor"/>
  </view>
</template>

<script>
import { ref, computed, onMounted, toRefs, watch } from 'vue'

import Case1 from "@/floors/case1"
import Toast from "@/floors/toast"
import Styledemo from "@/floors/styledemo"
import './index.scss'

export default {
  components:{
    Case1,
    Toast,
    Styledemo,
  },
  setup () {
    // const msg = ref('Hello Vue3')
    const username = ref('user1')
    const count = ref(0)
    const color = ref('blue')
    const changeColor = ()=>{
      color.value = 'red'
    }

    return {
      username,
      // msg,
      changeColor,
      color,
    }
  },
  onReady () {
    console.log('onReady')
  }
}
</script>
<style lang="scss">
.index{
  padding: 20px;
  h3{
  font-size: 30px;
}
}

</style>
