<template>
  <TablerIconSend :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconSend } from './IconHelper';

export default {
  name: 'IconSend',
  components: {
    TablerIconSend: IconSend
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