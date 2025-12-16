<template>
  <TablerIconDashboard :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconDashboard } from './IconHelper';

export default {
  name: 'IconDashboard',
  components: {
    TablerIconDashboard: IconDashboard
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