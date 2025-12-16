<template>
  <TablerIconDocument :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconDocument } from './IconHelper';

export default {
  name: 'IconDocument',
  components: {
    TablerIconDocument: IconDocument
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