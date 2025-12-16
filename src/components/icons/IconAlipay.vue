<template>
  <TablerIconAlipay :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconAlipay } from './IconHelper';

export default {
  name: 'IconAlipay',
  components: {
    TablerIconAlipay: IconAlipay
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