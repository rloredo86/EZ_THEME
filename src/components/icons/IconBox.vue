<template>
  <TablerIconBox :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconBox } from './IconHelper';

export default {
  name: 'IconBox',
  components: {
    TablerIconBox: IconBox
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