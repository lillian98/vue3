<template>
  <view>
    <view>count:{{ count }},msg:{{ info }}</view>
    <button @tap="incAndChangeInfo">
      增加 1修改msg
    </button>
  </view>
  
</template>
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