<template>
  <TablerIconCredit :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconCredit } from './IconHelper';

export default {
  name: 'IconCredit',
  components: {
    TablerIconCredit: IconCredit
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