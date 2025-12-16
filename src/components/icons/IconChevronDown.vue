<template>
  <TablerIconChevronDown :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconChevronDown } from './IconHelper';

export default {
  name: 'IconChevronDown',
  components: {
    TablerIconChevronDown: IconChevronDown
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