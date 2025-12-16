<template>
  <div class="loading-spinner" :class="{ 'loading-spinner-overlay': overlay }">
    <div class="spinner">
      <div class="spinner-circle"></div>
    </div>
    <div v-if="text" class="spinner-text">{{ text }}</div>
  </div>
</template>

<script>
export default {
  name: 'LoadingSpinner',
  props: {
    text: {
      type: String,
      default: ''
    },
    overlay: {
      type: Boolean,
      default: false
    }
  }
};
</script>

<style lang="scss" scoped>
@use "@/assets/styles/base/variables.scss" as *;

.loading-spinner {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  
  &-overlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-color: rgba(var(--card-background-rgb), 0.7);
    backdrop-filter: blur(5px);
    z-index: 9999;
  }
}

.spinner {
  width: 40px;
  height: 40px;
  position: relative;
  
  &-circle {
    width: 100%;
    height: 100%;
    border: 3px solid rgba(var(--theme-color-rgb), 0.2);
    border-top-color: var(--theme-color);
    border-radius: 50%;
    animation: spin 1s linear infinite;
  }
  
  &-text {
    margin-top: 1.5rem;
    color: var(--text-color);
    font-size: $font-size-sm;
  }
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style> 