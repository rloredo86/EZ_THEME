<template>
  <TablerIconInfo :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconInfo } from './IconHelper';

export default {
  name: 'IconInfo',
  components: {
    TablerIconInfo: IconInfo
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