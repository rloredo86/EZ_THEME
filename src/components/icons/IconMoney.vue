<template>
  <TablerIconMoney :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconMoney } from './IconHelper';

export default {
  name: 'IconMoney',
  components: {
    TablerIconMoney: IconMoney
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