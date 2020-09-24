<template>
  <button @click="showToast" class="btn">打开弹窗</button>  
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