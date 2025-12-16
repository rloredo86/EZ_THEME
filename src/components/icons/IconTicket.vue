<template>
  <TablerIconTicket :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconTicket } from './IconHelper';

export default {
  name: 'IconTicket',
  components: {
    TablerIconTicket: IconTicket
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