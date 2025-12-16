<template>
  <TablerIconCrown :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconCrown } from './IconHelper';

export default {
  name: 'IconCrown',
  components: {
    TablerIconCrown: IconCrown
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