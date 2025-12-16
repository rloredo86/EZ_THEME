<template>
  <button 
    class="theme-toggle" 
    :class="{ 'theme-toggle-dark': isDarkTheme }" 
    @click="toggleTheme"
    :title="$t('common.toggleTheme')"
  >
    <div class="theme-toggle-icon">
      <svg v-if="isDarkTheme" xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
        <circle cx="12" cy="12" r="5"></circle>
        <line x1="12" y1="1" x2="12" y2="3"></line>
        <line x1="12" y1="21" x2="12" y2="23"></line>
        <line x1="4.22" y1="4.22" x2="5.64" y2="5.64"></line>
        <line x1="18.36" y1="18.36" x2="19.78" y2="19.78"></line>
        <line x1="1" y1="12" x2="3" y2="12"></line>
        <line x1="21" y1="12" x2="23" y2="12"></line>
        <line x1="4.22" y1="19.78" x2="5.64" y2="18.36"></line>
        <line x1="18.36" y1="5.64" x2="19.78" y2="4.22"></line>
      </svg>
      <svg v-else xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
        <path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"></path>
      </svg>
    </div>
  </button>
</template>

<script>
import { computed } from 'vue';
import { useTheme } from '@/composables/useTheme';

export default {
  name: 'ThemeToggle',
  setup() {
    const { theme, toggleTheme } = useTheme();
    
    const isDarkTheme = computed(() => theme.value === 'dark');
    
    return {
      isDarkTheme,
      toggleTheme
    };
  }
};
</script>

<style lang="scss" scoped>
.theme-toggle {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: var(--card-background);
  border: 1px solid var(--border-color);
  color: var(--text-color);
  cursor: pointer;
  transition: all 0.3s ease;
  padding: 0;
  overflow: hidden;
  
  &:hover {
    background-color: rgba(var(--theme-color-rgb), 0.1);
    border-color: var(--theme-color);
    transform: translateY(-2px);
    box-shadow: 0 3px 8px rgba(0, 0, 0, 0.15);
    color: var(--theme-color);
  }
  
  &:active {
    transform: translateY(0);
    transition: all 0.1s ease;
  }
  
  &-icon {
    display: flex;
    align-items: center;
    justify-content: center;
    
    svg {
      transition: transform 0.3s ease;
    }
  }
  
  &:hover &-icon svg {
    transform: rotate(20deg);
  }
}
</style> 