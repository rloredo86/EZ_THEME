<template>
  <TablerIconArrowUp :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconArrowUp } from './IconHelper';

export default {
  name: 'IconArrowUp',
  components: {
    TablerIconArrowUp: IconArrowUp
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