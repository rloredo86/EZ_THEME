<template>
  <TablerIconEye :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconEye } from './IconHelper';

export default {
  name: 'IconEye',
  components: {
    TablerIconEye: IconEye
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