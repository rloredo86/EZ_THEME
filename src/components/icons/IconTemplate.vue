<template>
  <component :is="icon" :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
// 替换下面的导入
import { IconName } from './IconHelper';

export default {
  name: 'IconComponentName', // 需要修改为实际组件名
  components: {
    // 替换成实际图标名
    icon: IconName
  },
  props: {
    size: {
      type: [Number, String],
      default: 24
    },
    strokeWidth: {
      type: [Number, String],
      default: 2
    },
    customClass: {
      type: String,
      default: ''
    }
  }
};
</script> 