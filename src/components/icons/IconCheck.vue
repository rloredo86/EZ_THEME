<template>
  <TablerIconCheck :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconCheck } from './IconHelper';

export default {
  name: 'IconCheck',
  components: {
    TablerIconCheck: IconCheck
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