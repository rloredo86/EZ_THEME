<template>
  <TablerIconLink :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconLink } from './IconHelper';

export default {
  name: 'IconLink',
  components: {
    TablerIconLink: IconLink
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