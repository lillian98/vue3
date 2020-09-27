<template>
  <button @tap="showToast" class="btn">打开弹窗</button>  
  <!-- to 和 index 中的 view id一致 -->
  <teleport to="#teleportToast">
    <view v-if="toastFlag" class="toast__wrap"  @tap="hideToast">
      <view class="h2">弹窗标题：</view>
      <view class="toast__wrap--msg">欢迎{{ user }}，点击关闭</view>
    </view>
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
import { ref,toRefs } from 'vue'
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