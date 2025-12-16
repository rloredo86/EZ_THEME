<template>
  <transition name="context-menu">
    <div 
      v-if="show" 
      class="custom-context-menu" 
      :style="{ top: position.y + 'px', left: position.x + 'px' }"
      @click.stop
    >
      <div class="menu-items">
        <div class="menu-item" @click="handleCopy" v-if="canCopy">
          <div class="menu-icon">
            <svg viewBox="0 0 24 24" width="18" height="18" stroke="currentColor" stroke-width="2" fill="none" stroke-linecap="round" stroke-linejoin="round">
              <rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
              <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path>
            </svg>
          </div>
          <div class="menu-text">{{ $t('contextMenu.copy') }}</div>
        </div>
        <div class="menu-divider" v-if="canCopy && (canRefresh || canNavigate)"></div>
        <div class="menu-item" @click="handleRefresh">
          <div class="menu-icon">
            <svg viewBox="0 0 24 24" width="18" height="18" stroke="currentColor" stroke-width="2" fill="none" stroke-linecap="round" stroke-linejoin="round">
              <path d="M23 4v6h-6"></path>
              <path d="M1 20v-6h6"></path>
              <path d="M3.51 9a9 9 0 0 1 14.85-3.36L23 10M1 14l4.64 4.36A9 9 0 0 0 20.49 15"></path>
            </svg>
          </div>
          <div class="menu-text">{{ $t('contextMenu.refresh') }}</div>
        </div>
        <div class="menu-item" @click="handleBack">
          <div class="menu-icon">
            <svg viewBox="0 0 24 24" width="18" height="18" stroke="currentColor" stroke-width="2" fill="none" stroke-linecap="round" stroke-linejoin="round">
              <path d="M19 12H5"></path>
              <path d="M12 19l-7-7 7-7"></path>
            </svg>
          </div>
          <div class="menu-text">{{ $t('contextMenu.back') }}</div>
        </div>
        <div class="menu-item" @click="handleForward">
          <div class="menu-icon">
            <svg viewBox="0 0 24 24" width="18" height="18" stroke="currentColor" stroke-width="2" fill="none" stroke-linecap="round" stroke-linejoin="round">
              <path d="M5 12h14"></path>
              <path d="M12 5l7 7-7 7"></path>
            </svg>
          </div>
          <div class="menu-text">{{ $t('contextMenu.forward') }}</div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import { ref, onMounted, onUnmounted } from 'vue';
import { useRouter } from 'vue-router';

export default {
  name: 'CustomContextMenu',
  setup() {
    const show = ref(false);
    const position = ref({ x: 0, y: 0 });
    const router = useRouter();
    
    // 是否可以复制、导航
    const canCopy = ref(false);
    const canRefresh = ref(true);
    const canNavigate = ref(true);
    
    // 处理右键点击事件
    const handleContextMenu = (event) => {
      event.preventDefault();
      
      // 检查是否可以复制
      // 如果选中了文本或在输入框中，则可以复制
      const selection = window.getSelection();
      canCopy.value = selection.toString().length > 0;
      
      // 计算菜单位置，防止超出视窗
      const x = Math.min(event.clientX, window.innerWidth - 200);
      const y = Math.min(event.clientY, window.innerHeight - 160);
      
      position.value = { x, y };
      show.value = true;
      
      // 添加菜单展开动画类
      setTimeout(() => {
        const menuItems = document.querySelectorAll('.menu-item');
        menuItems.forEach((item, index) => {
          setTimeout(() => {
            item.classList.add('appear');
          }, index * 50);
        });
      }, 50);
    };
    
    // 处理点击外部关闭菜单
    const handleClickOutside = () => {
      if (show.value) {
        show.value = false;
        
        // 移除动画类
        const menuItems = document.querySelectorAll('.menu-item');
        menuItems.forEach(item => {
          item.classList.remove('appear');
        });
      }
    };
    
    // 复制功能
    const handleCopy = async () => {
      try {
        const selection = window.getSelection();
        if (selection.toString().length > 0) {
          await navigator.clipboard.writeText(selection.toString());
        }
      } catch (err) {
        console.error('复制失败:', err);
      }
      handleClickOutside();
    };
    
    // 刷新页面
    const handleRefresh = () => {
      window.location.reload();
    };
    
    // 返回上一页
    const handleBack = () => {
      router.back();
      handleClickOutside();
    };
    
    // 前进到下一页
    const handleForward = () => {
      router.forward();
      handleClickOutside();
    };
    
    onMounted(() => {
      // 添加右键和点击事件监听
      document.addEventListener('contextmenu', handleContextMenu);
      document.addEventListener('click', handleClickOutside);
      
      // 窗口大小改变时隐藏菜单
      window.addEventListener('resize', handleClickOutside);
      
      // 滚动时隐藏菜单
      window.addEventListener('scroll', handleClickOutside);
    });
    
    onUnmounted(() => {
      // 移除事件监听
      document.removeEventListener('contextmenu', handleContextMenu);
      document.removeEventListener('click', handleClickOutside);
      window.removeEventListener('resize', handleClickOutside);
      window.removeEventListener('scroll', handleClickOutside);
    });
    
    return {
      show,
      position,
      canCopy,
      canRefresh,
      canNavigate,
      handleCopy,
      handleRefresh,
      handleBack,
      handleForward
    };
  }
};
</script>

<style lang="scss" scoped>
.custom-context-menu {
  position: fixed;
  z-index: 1000;
  min-width: 180px;
  background-color: rgba(var(--card-background-rgb), 0.6);
  backdrop-filter: blur(15px);
  -webkit-backdrop-filter: blur(15px);
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1), 0 5px 10px rgba(0, 0, 0, 0.05);
  border: 1px solid rgba(var(--theme-color-rgb), 0.1);
  padding: 8px;
  transform-origin: top left;
}

.menu-items {
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.menu-item {
  display: flex;
  align-items: center;
  padding: 10px 14px;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.2s ease;
  opacity: 0;
  transform: translateY(10px);
  
  &.appear {
    opacity: 1;
    transform: translateY(0);
  }
  
  &:hover {
    background-color: rgba(var(--theme-color-rgb), 0.1);
    
    .menu-icon {
      color: var(--theme-color);
      transform: scale(1.1);
    }
  }
  
  &:active {
    background-color: rgba(var(--theme-color-rgb), 0.2);
    transform: scale(0.98);
  }
}

.menu-divider {
  height: 1px;
  background-color: rgba(var(--theme-color-rgb), 0.1);
  margin: 4px 0;
}

.menu-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 36px;
  height: 36px;
  border-radius: 8px;
  margin-right: 12px;
  background-color: rgba(var(--theme-color-rgb), 0.08);
  color: var(--text-color);
  transition: all 0.3s ease;
}

.menu-text {
  font-size: 14px;
  font-weight: 500;
  color: var(--text-color);
}

/* 动画效果 */
.context-menu-enter-active,
.context-menu-leave-active {
  transition: transform 0.2s ease, opacity 0.2s ease;
}

.context-menu-enter-from,
.context-menu-leave-to {
  transform: scale(0.95);
  opacity: 0;
}

/* 深色模式适配 */
:deep(.dark-theme) {
  .custom-context-menu {
    background-color: rgba(30, 30, 32, 0.8);
    border-color: rgba(255, 255, 255, 0.05);
  }
  
  .menu-icon {
    background-color: rgba(255, 255, 255, 0.05);
  }
}
</style> 