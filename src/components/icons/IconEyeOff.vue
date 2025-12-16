<template>
  <TablerIconEyeOff :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconEyeOff } from './IconHelper';

export default {
  name: 'IconEyeOff',
  components: {
    TablerIconEyeOff: IconEyeOff
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