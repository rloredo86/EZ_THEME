<template>
  <TablerIconMore :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconMore } from './IconHelper';

export default {
  name: 'IconMore',
  components: {
    TablerIconMore: IconMore
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