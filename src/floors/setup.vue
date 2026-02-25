<template>
  <view>
    <view>count:{{ count }},msg:{{ info }}</view>
    <button @tap="incAndChangeInfo">
      增加 1修改msg
    </button>
  </view>
  
</template>
<script setup>
import { ref } from 'vue'

const props = defineProps({
  msg: String,
})

const count = ref(0)
const info = ref(props.msg)
const incAndChangeInfo = () => {
  count.value++
  info.value = "change hello" + count.value
}
</script>