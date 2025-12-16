<template>
  <TablerIconMail :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconMail } from './IconHelper';

export default {
  name: 'IconMail',
  components: {
    TablerIconMail: IconMail
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