<template>
  <TablerIconTwitter :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconTwitter } from './IconHelper';

export default {
  name: 'IconTwitter',
  components: {
    TablerIconTwitter: IconTwitter
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