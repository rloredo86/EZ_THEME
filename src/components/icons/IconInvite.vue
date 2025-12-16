<template>
  <TablerIconInvite :size="size" :stroke-width="strokeWidth" :class="customClass" />
</template>

<script>
import { IconInvite } from './IconHelper';

export default {
  name: 'IconInvite',
  components: {
    TablerIconInvite: IconInvite
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