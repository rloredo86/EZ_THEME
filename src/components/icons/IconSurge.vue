<template>
  <TablerIconSurge :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconSurge } from './IconHelper';

export default {
  name: 'IconSurge',
  components: {
    TablerIconSurge: IconSurge
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