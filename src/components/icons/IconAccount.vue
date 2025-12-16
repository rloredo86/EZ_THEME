<template>
  <TablerIconAccount :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconAccount } from './IconHelper';

export default {
  name: 'IconAccount',
  components: {
    TablerIconAccount: IconAccount
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