<template>
  <TablerIconArrowRight :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconArrowRight } from './IconHelper';

export default {
  name: 'IconArrowRight',
  components: {
    TablerIconArrowRight: IconArrowRight
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