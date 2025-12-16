<template>
  <TablerIconLogout :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconLogout } from './IconHelper';

export default {
  name: 'IconLogout',
  components: {
    TablerIconLogout: IconLogout
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