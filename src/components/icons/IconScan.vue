<template>
  <TablerIconScan :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconScan } from './IconHelper';

export default {
  name: 'IconScan',
  components: {
    TablerIconScan: IconScan
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