<template>
  <TablerIconStorage :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconStorage } from './IconHelper';

export default {
  name: 'IconStorage',
  components: {
    TablerIconStorage: IconStorage
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