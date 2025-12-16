<template>
  <TablerIconCode :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconCode } from './IconHelper';

export default {
  name: 'IconCode',
  components: {
    TablerIconCode: IconCode
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