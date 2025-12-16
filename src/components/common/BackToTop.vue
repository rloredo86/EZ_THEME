<template>
  <transition name="fade">
    <div 
      v-show="visible && !isMobileDevice" 
      class="back-to-top"
      @click="scrollToTop"
      :class="{ 'dark': isDarkTheme }"
    >
      <IconChevronsUp :size="20" :stroke-width="1.5" class="icon" />
    </div>
  </transition>
</template>

<script>
import { ref, onMounted, onUnmounted, computed } from 'vue';
import { useStore } from 'vuex';
import { IconChevronsUp } from '@tabler/icons-vue';

export default {
  name: 'BackToTop',
  components: {
    IconChevronsUp
  },
  setup() {
    const visible = ref(false);
    const store = useStore();
    const isDarkTheme = computed(() => store.getters.currentTheme === 'dark');
    const isMobileDevice = ref(false);

    // 检查设备类型
    const checkDeviceType = () => {
      isMobileDevice.value = window.innerWidth < 770;
    };

    // 检查滚动位置
    const checkScroll = () => {
      visible.value = window.scrollY > 100;
    };

    // 平滑滚动到顶部
    const scrollToTop = () => {
      // 首先立即跳转到80%的位置，给用户即时反馈
      const currentPosition = window.pageYOffset;
      window.scrollTo(0, currentPosition * 0.8);
      
      // 然后使用简单的步进滚动到顶部
      let currentPos = window.pageYOffset;
      const scrollInterval = setInterval(() => {
        if (currentPos <= 0) {
          clearInterval(scrollInterval);
          window.scrollTo(0, 0);
          return;
        }
        
        // 使用指数减速，每次滚动剩余距离的30%
        const scrollStep = Math.max(currentPos * 0.3, 10);
        currentPos = currentPos - scrollStep;
        window.scrollTo(0, currentPos);
      }, 15); // 15ms的间隔，约等于60fps
    };

    // 监听滚动事件
    onMounted(() => {
      window.addEventListener('scroll', checkScroll, { passive: true });
      window.addEventListener('resize', checkDeviceType, { passive: true });
      
      // 初始检查
      checkScroll();
      checkDeviceType();
    });

    onUnmounted(() => {
      window.removeEventListener('scroll', checkScroll);
      window.removeEventListener('resize', checkDeviceType);
    });

    return {
      visible,
      isMobileDevice,
      scrollToTop,
      isDarkTheme
    };
  }
};
</script>

<style lang="scss" scoped>
.back-to-top {
  position: fixed;
  right: 30px;
  bottom: 30px;
  width: 42px;
  height: 42px;
  border-radius: 12px;
  background-color: var(--theme-color);
  box-shadow: 0 4px 15px rgba(var(--theme-color-rgb), 0.35);
  cursor: pointer;
  z-index: 99999;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
  opacity: 0.95;
  -webkit-tap-highlight-color: transparent;
  overflow: hidden;
  
  &::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: linear-gradient(145deg, rgba(255, 255, 255, 0.2), rgba(255, 255, 255, 0));
    opacity: 0;
    transition: opacity 0.3s ease;
    border-radius: 12px;
  }
  
  &::after {
    content: '';
    position: absolute;
    top: 1px;
    left: 1px;
    right: 1px;
    height: 50%;
    background: linear-gradient(to bottom, rgba(255, 255, 255, 0.2), rgba(255, 255, 255, 0));
    border-radius: 11px 11px 50% 50%;
    opacity: 0.6;
  }
  
  &:hover {
    transform: translateY(-3px);
    box-shadow: 0 8px 20px rgba(var(--theme-color-rgb), 0.45);
    opacity: 1;
    
    &::before {
      opacity: 1;
    }
    
    .icon {
      transform: translateY(-2px) scale(1.1);
      filter: drop-shadow(0 2px 2px rgba(0, 0, 0, 0.1));
    }
  }
  
  &:active {
    transform: translateY(0);
    transition: all 0.1s ease;
    box-shadow: 0 2px 10px rgba(var(--theme-color-rgb), 0.35);
    
    .icon {
      transform: translateY(0) scale(0.95);
      transition: all 0.1s ease;
    }
  }
  
  .icon {
    width: 22px;
    height: 22px;
    color: white;
    filter: drop-shadow(0 1px 1px rgba(0, 0, 0, 0.1));
    transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
    position: relative;
    z-index: 2;
  }
  
  &.dark {
    background: linear-gradient(145deg, var(--theme-color), rgba(var(--theme-color-rgb), 0.7));
    box-shadow: 0 2px 12px rgba(var(--theme-color-rgb), 0.45);
    
    &:hover {
      box-shadow: 0 8px 20px rgba(var(--theme-color-rgb), 0.5);
    }
    
    &::before {
      background: linear-gradient(145deg, rgba(255, 255, 255, 0.15), rgba(255, 255, 255, 0));
    }
  }
}

// 淡入淡出动画
.fade-enter-active,
.fade-leave-active {
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: scale(0.9) translateY(20px);
}

/* 移动端适配 */
@media (max-width: 768px) {
  .back-to-top {
    right: 16px;
    bottom: 100px;
    width: 40px;
    height: 40px;
    border-radius: 10px;
    
    .icon {
      width: 20px;
      height: 20px;
    }
    
    &:hover {
      transform: translateY(-2px);
      box-shadow: 0 6px 16px rgba(var(--theme-color-rgb), 0.35);
      
      .icon {
        transform: translateY(-1px) scale(1.05);
      }
    }
  }
}

/* 特小屏幕适配 */
@media (max-width: 480px) {
  .back-to-top {
    right: 12px;
    bottom: 90px;
    width: 36px;
    height: 36px;
    
    .icon {
      width: 18px;
      height: 18px;
    }
  }
}
</style> 