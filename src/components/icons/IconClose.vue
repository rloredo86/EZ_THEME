<template>
  <TablerIconClose :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconClose } from './IconHelper';

export default {
  name: 'IconClose',
  components: {
    TablerIconClose: IconClose
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