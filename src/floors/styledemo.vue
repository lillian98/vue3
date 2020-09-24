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