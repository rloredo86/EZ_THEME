<template>
  <TablerIconSubscription :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconSubscription } from './IconHelper';

export default {
  name: 'IconSubscription',
  components: {
    TablerIconSubscription: IconSubscription
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