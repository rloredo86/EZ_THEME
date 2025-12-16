<template>
  <TablerIconFileText :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconFileText } from './IconHelper';

export default {
  name: 'IconFileText',
  components: {
    TablerIconFileText: IconFileText
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