<template>
  <div class="slide-tabs-container">
    <div class="slide-tabs-wrapper">
      <div class="slide-tabs-nav" ref="tabsNav">
        <template v-for="(item, index) in navItems" :key="`${item.name}-${languageKey}`">
          <router-link 
            :to="item.path"
            class="nav-item"
            :class="{ 'active': currentRoute === item.name || (route && route.meta && route.meta.activeNav === item.name) }"
            @click.prevent="navigateTo(item, index)"
          >
            <div class="nav-icon">
              <component :is="getIcon(item.icon)" />
            </div>
            <span class="nav-text">{{ $t(`menu.${item.i18nKey}`) }}</span>
            <span v-if="item.name === 'Invite' && showInviteBadge" class="badge-dot">{{ $t('menu.commission') }}</span>
            <span v-if="item.name === 'Shop' && showShopBadge" class="badge-dot">{{ $t('menu.hotSale') }}</span>
          </router-link>
        </template>
        <div class="indicator-container">
          <div class="slider-indicator" :style="sliderStyle"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, watch, nextTick, onBeforeUnmount, computed, reactive, onUnmounted } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import { INVITE_CONFIG, SHOP_CONFIG } from '@/utils/baseConfig';
import IconDashboard from '@/components/icons/IconDashboard.vue';
import IconShop from '@/components/icons/IconShop.vue';
import IconInvite from '@/components/icons/IconInvite.vue';
import IconMore from '@/components/icons/IconMore.vue';

export default {
  name: 'SlideTabsNav',
  setup() {
    const route = useRoute();
    const router = useRouter();
    const tabsNav = ref(null);
    const currentRoute = ref('');
    const currentIndex = ref(0);
    const previousIndex = ref(0);
    const isInitialized = ref(false);
    const isTransitionEnabled = ref(false);
    const isComponentMounted = ref(false);
    const languageKey = ref(Date.now());
    
    // 检查是否显示邀请红点
    const showInviteBadge = computed(() => {
      return INVITE_CONFIG && INVITE_CONFIG.showCommissionBadge === true;
    });
    
    // 检查是否显示商店红点
    const showShopBadge = computed(() => {
      return SHOP_CONFIG && SHOP_CONFIG.showHotSaleBadge === true;
    });

    // 使用响应式对象存储滑块样式
    const sliderState = reactive({
      width: 0,
      transform: 'translateX(0px)',
      opacity: 0,
      transition: 'none'
    });
    
    // 计算滑块样式
    const sliderStyle = computed(() => ({
      width: `${sliderState.width}px`,
      transform: sliderState.transform,
      opacity: sliderState.opacity,
      transition: sliderState.transition
    }));

    const navItems = [
      { title: 'Dashboard', path: '/dashboard', name: 'Dashboard', icon: 'IconDashboard', i18nKey: 'dashboard' },
      { title: 'Shop', path: '/shop', name: 'Shop', icon: 'IconShop', i18nKey: 'shop' },
      { title: 'Invite', path: '/invite', name: 'Invite', icon: 'IconInvite', i18nKey: 'invite' },
      { title: 'More', path: '/more', name: 'More', icon: 'IconMore', i18nKey: 'more' },
    ];
    
    // 获取图标组件
    const getIcon = (iconName) => {
      switch(iconName) {
        case 'IconDashboard': return IconDashboard;
        case 'IconShop': return IconShop;
        case 'IconInvite': return IconInvite;
        case 'IconMore': return IconMore;
        default: return null;
      }
    };
    
    // 定时器引用，用于清理
    let positionTimers = [];

    // 安全地导航到路由
    const navigateTo = (item, index) => {
      if (!isComponentMounted.value) return;
      
      previousIndex.value = currentIndex.value;
      currentIndex.value = index;
      updateSliderPosition(index, true);
      
      // 使用 nextTick 确保 DOM 更新后再导航
      nextTick(() => {
        if (isComponentMounted.value) {
          router.push(item.path);
        }
      });
    };

    const updateSliderPosition = (index, animate = true) => {
      // 如果组件已卸载或没有引用，则不执行
      if (!isComponentMounted.value || index < 0 || !tabsNav.value) return;
      
      // 清除所有未完成的定时器
      positionTimers.forEach(timer => clearTimeout(timer));
      positionTimers = [];
      
      // 使用 try-catch 包装以防止任何引用错误
      try {
        nextTick(() => {
          // 再次检查组件状态
          if (!isComponentMounted.value || !tabsNav.value) return;
          
          const navItemElements = tabsNav.value.querySelectorAll('.nav-item');
          
          if (navItemElements.length > 0 && index >= 0 && index < navItemElements.length) {
            const activeItem = navItemElements[index];
            const { offsetWidth, offsetLeft } = activeItem;
            
            // 设置过渡效果 - 始终使用动画，除非明确禁用
            if (animate) {
              sliderState.transition = 'all 0.6s cubic-bezier(0.25, 0.1, 0.25, 1)';
            } else {
              sliderState.transition = 'none';
            }
            
            // 更新滑块位置和大小
            sliderState.width = offsetWidth;
            sliderState.transform = `translateX(${offsetLeft}px)`;
            
            // 确保滑块可见
            sliderState.opacity = 1;
            
            // 设置已初始化标志
            isInitialized.value = true;
          }
        });
      } catch (error) {
        console.warn('SlideTabsNav: Error updating slider position', error);
      }
    };

    const findIndexByRouteName = (routeName) => {
      // 检查当前路由是否有activeNav元数据
      if (route && route.meta && route.meta.activeNav) {
        // 如果有，则使用activeNav指定的导航项名称
        const activeNavName = route.meta.activeNav;
        const activeIndex = navItems.findIndex(item => item.name === activeNavName);
        
        if (activeIndex !== -1) {
          return activeIndex;
        }
      }
      
      // 如果没有activeNav或找不到对应的导航项，则按原来的逻辑处理
      const index = navItems.findIndex(item => item.name === routeName);
      return index !== -1 ? index : 0; // 如果找不到，默认返回第一个
    };
    
    // 窗口大小变化处理函数
    const handleResize = () => {
      if (isComponentMounted.value) {
        // 不使用动画立即更新滑块位置
        updateSliderPosition(currentIndex.value, false);
        
        // 短暂延迟后再次更新，这次使用动画
        safeTimeout(() => {
          updateSliderPosition(currentIndex.value, true);
        }, 200);
      }
    };

    // 监听路由变化
    let stopRouteWatch = null;
    
    // 监听组件的可见性变化
    const handleVisibilityChange = () => {
      if (!isComponentMounted.value) return;
      
      if (!document.hidden && currentIndex.value >= 0) {
        // 页面可见时重新计算滑块位置
        updateSliderPosition(currentIndex.value, false);
        
        // 再次更新以确保位置准确
        if (isComponentMounted.value) {
          const timer = setTimeout(() => {
            if (isComponentMounted.value) {
              updateSliderPosition(currentIndex.value, true);
            }
          }, 200);
          
          positionTimers.push(timer);
        }
      }
    };

    // 安全地创建定时器，确保只有在组件挂载状态下执行回调
    const safeTimeout = (callback, delay) => {
      const timer = setTimeout(() => {
        if (isComponentMounted.value) {
          callback();
        }
      }, delay);
      
      positionTimers.push(timer);
      return timer;
    };

    // 添加自动检查位置的功能
    let positionCheckInterval = null;
    
    // 检查并修复滑块位置
    const checkAndFixSliderPosition = () => {
      if (!isComponentMounted.value || !tabsNav.value) return;
      
      try {
        const navItemElements = tabsNav.value.querySelectorAll('.nav-item');
        
        // 优先使用activeNav元数据确定活动索引
        let activeIndex;
        if (route && route.meta && route.meta.activeNav) {
          const activeNavName = route.meta.activeNav;
          const indexByActiveNav = navItems.findIndex(item => item.name === activeNavName);
          if (indexByActiveNav !== -1) {
            activeIndex = indexByActiveNav;
          } else {
            activeIndex = findIndexByRouteName(route.name);
          }
        } else {
          activeIndex = findIndexByRouteName(route.name);
        }
        
        if (navItemElements.length > 0 && activeIndex >= 0 && activeIndex < navItemElements.length) {
          const activeItem = navItemElements[activeIndex];
          const { offsetWidth, offsetLeft } = activeItem;
          
          // 检查当前滑块位置是否与活动项匹配
          const currentTransform = sliderState.transform;
          const expectedTransform = `translateX(${offsetLeft}px)`;
          
          // 如果位置不匹配或宽度不匹配，修复它
          if (currentTransform !== expectedTransform || sliderState.width !== offsetWidth) {
            // 使用动画更新位置
            updateSliderPosition(activeIndex, true);
          }
        }
      } catch (error) {
        console.warn('SlideTabsNav: Error checking slider position', error);
      }
    };
    
    // 处理滚动事件
    const handleScroll = debounce(() => {
      if (isComponentMounted.value) {
        checkAndFixSliderPosition();
      }
    }, 200);
    
    // 监听语言变化事件
    const onLanguageChanged = () => {
      languageKey.value = Date.now();
      
      // 在语言变化后，添加平滑的宽度过渡
      sliderState.transition = 'width 0.6s cubic-bezier(0.25, 0.1, 0.25, 1), transform 0.6s cubic-bezier(0.25, 0.1, 0.25, 1), opacity 0.3s ease';
      
      // 先保持当前位置和不可见状态，等待导航项文本更新
      sliderState.opacity = 0.3;
      
      // 使用多阶段更新策略，在文本更新后再逐步调整滑块
      [50, 200, 400, 600].forEach(delay => {
        safeTimeout(() => {
          if (isComponentMounted.value && tabsNav.value) {
            // 逐步恢复不透明度
            if (delay >= 400) {
              sliderState.opacity = 1;
            }
            updateSliderPosition(currentIndex.value, true);
          }
        }, delay);
      });
    };

    onMounted(() => {
      // 标记组件已挂载
      isComponentMounted.value = true;
      
      // 添加可见性变化监听器
      document.addEventListener('visibilitychange', handleVisibilityChange);
      
      // 添加语言变化事件监听 - 修正事件名称
      window.addEventListener('languageChanged', onLanguageChanged);
      
      // 初始化时禁用过渡效果
      isTransitionEnabled.value = false;
      
      // 初始化路由监听
      stopRouteWatch = watch(() => route.name, (newRoute) => {
        if (!isComponentMounted.value) return;
        
        if (newRoute) {
          // 设置当前路由名称
          currentRoute.value = newRoute;
          
          // 优先使用activeNav元数据确定滑块位置
          let newIndex;
          if (route.meta && route.meta.activeNav) {
            const activeNavName = route.meta.activeNav;
            const indexByActiveNav = navItems.findIndex(item => item.name === activeNavName);
            if (indexByActiveNav !== -1) {
              newIndex = indexByActiveNav;
            } else {
              newIndex = findIndexByRouteName(newRoute);
            }
          } else {
            newIndex = findIndexByRouteName(newRoute);
          }
          
          previousIndex.value = currentIndex.value;
          currentIndex.value = newIndex;
          
          // 使用动画更新位置
          updateSliderPosition(newIndex, true);
        }
      }, { immediate: true });
      
      // 初始化滑块位置
      nextTick(() => {
        if (!isComponentMounted.value) return;
        
        const index = findIndexByRouteName(route.name);
        updateSliderPosition(index, false);
        
        // 延迟启用过渡效果
        safeTimeout(() => {
          isTransitionEnabled.value = true;
        }, 300);
      });
      
      // 多次尝试更新位置，确保初始化成功
      [100, 300, 500, 1000].forEach(delay => {
        safeTimeout(() => {
          if (tabsNav.value) {
            const index = findIndexByRouteName(route.name);
            updateSliderPosition(index, delay > 300);
          }
        }, delay);
      });
      
      // 添加额外的延迟检查，确保页面完全加载后正确定位
      safeTimeout(() => {
        if (isComponentMounted.value && tabsNav.value) {
          const index = findIndexByRouteName(route.name);
          // 强制重新计算位置，不使用动画
          updateSliderPosition(index, false);
          // 再次更新，使用动画
          safeTimeout(() => {
            updateSliderPosition(index, true);
          }, 50);
        }
      }, 1500);
      
      // 等待页面完全加载后的额外检查
      window.addEventListener('load', () => {
        if (isComponentMounted.value && tabsNav.value) {
          const index = findIndexByRouteName(route.name);
          updateSliderPosition(index, false);
          safeTimeout(() => {
            updateSliderPosition(index, true);
          }, 100);
        }
      });
      
      // 添加窗口大小变化监听器，使用防抖优化性能
      const debouncedResize = debounce(handleResize, 100);
      window.addEventListener('resize', debouncedResize);
      
      // 添加滚动监听
      window.addEventListener('scroll', handleScroll, { passive: true });
      
      // 初始化周期性位置检查 - 每3秒检查一次
      positionCheckInterval = setInterval(() => {
        if (isComponentMounted.value) {
          checkAndFixSliderPosition();
        }
      }, 3000);
      
      // 初始化完成后，重新定位一次以确保正确
      safeTimeout(() => {
        handleResize();
      }, 1500);
      
      // 从非菜单页面导航到菜单页面时立即更新滑块位置
      window.addEventListener('popstate', () => {
        if (isComponentMounted.value && tabsNav.value) {
          safeTimeout(() => {
            const index = findIndexByRouteName(route.name);
            updateSliderPosition(index, false);
            safeTimeout(() => {
              updateSliderPosition(index, true);
            }, 50);
          }, 0);
        }
      });
    });
    
    // 分开处理卸载清理，防止引用问题
    onUnmounted(() => {
      if (stopRouteWatch) {
        stopRouteWatch();
        stopRouteWatch = null;
      }
      
      // 清除周期性检查
      if (positionCheckInterval) {
        clearInterval(positionCheckInterval);
        positionCheckInterval = null;
      }
      
      // 移除popstate事件监听
      window.removeEventListener('popstate', () => {});
    });
    
    onBeforeUnmount(() => {
      // 标记组件已卸载
      isComponentMounted.value = false;
      
      // 清理所有定时器
      positionTimers.forEach(timer => clearTimeout(timer));
      positionTimers = [];
      
      // 移除事件监听器 - 修正事件名称
      document.removeEventListener('visibilitychange', handleVisibilityChange);
      window.removeEventListener('languageChanged', onLanguageChanged);
      window.removeEventListener('resize', handleResize);
      window.removeEventListener('scroll', handleScroll);
      
      // 清空全部引用
      tabsNav.value = null;
    });

    return {
      navItems,
      tabsNav,
      currentRoute,
      navigateTo,
      sliderStyle,
      getIcon,
      languageKey,
      route,
      showInviteBadge,
      showShopBadge
    };
  }
};

// 防抖函数的实现
function debounce(fn, delay) {
  let timer = null;
  return function(...args) {
    if (timer) clearTimeout(timer);
    timer = setTimeout(() => {
      fn.apply(this, args);
    }, delay);
  };
}
</script>

<style lang="scss" scoped>
.slide-tabs-container {
  margin-bottom: 20px;
  position: fixed;
  top: 20px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 10;
  width: auto;
  
  .slide-tabs-wrapper {
    background: rgba(var(--card-background-rgb), 0.7);
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
    border-radius: 30px;
    padding: 5px;
    box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
    border: 1px solid var(--border-color);
    display: inline-block;
    overflow: hidden;
  }

  .slide-tabs-nav {
    display: flex;
    position: relative;
    
    .indicator-container {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      pointer-events: none;
      z-index: 1;
    }
    
    .nav-item {
      padding: 6px 16px;
      border-radius: 26px;
      font-weight: 500;
      font-size: 14px;
      color: var(--secondary-text-color);
      text-decoration: none;
      text-align: center;
      position: relative;
      z-index: 2;
      transition: color 0.3s;
      white-space: nowrap;
      display: flex;
      align-items: center;
      gap: 5px;
      
      /* 添加红点样式 */
      .badge-dot {
        position: absolute;
        top: -3px;
        right: -2px;
        background-color:#ff3030;
        color: white;
        border-radius: 10px;
        padding: 1px 5px;
        font-size: 8px;
        font-weight: bold;
        line-height: 1.2;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
        transform: scale(0.9);
        white-space: nowrap;
      }
      
      &:last-child {
        // 为More按钮添加特殊样式
        border: 1px solid var(--theme-color);
        background-color: rgba(var(--theme-color-rgb), 0.05);
        
        .nav-icon svg {
          color: var(--theme-color);
        }
        
        &:hover {
          background-color: rgba(var(--theme-color-rgb), 0.1);
        }
        
        &.active {
          background-color: rgba(var(--theme-color-rgb), 0.15);
        }
      }
      
      /* 在桌面端也显示图标 */
      .nav-icon {
        display: flex;
        align-items: center;
        justify-content: center;
        
        svg {
          width: 18px;
          height: 18px;
          transition: color 0.3s ease, transform 0.3s ease;
          transform: scale(1);
        }
      }
      
      &.active {
        color: var(--text-color);
        
        .nav-icon svg {
          color: var(--theme-color);
          transform: scale(1.15);
        }
      }
      
      &:hover {
        color: var(--text-color);
      }
    }
    
    .slider-indicator {
      position: absolute;
      top: 0;
      left: 0;
      height: 100%;
      background-color: rgba(var(--theme-color-rgb), 0.1);
      border-radius: 26px;
      z-index: 1;
      box-shadow: 0 4px 15px rgba(var(--theme-color-rgb), 0.1);
      border: 1px solid var(--theme-color);
      will-change: transform, width, opacity;
      transition-property: transform, width, opacity;  /* 确保只有这些属性有过渡效果 */
    }
  }
}

// 响应式设计
@media (max-width: 768px) {
  .slide-tabs-container {
    .slide-tabs-nav {
      .nav-item {
        padding: 6px 10px;
        font-size: 12px;
        flex-direction: column;
        gap: 4px;
        justify-content: center;
        height: 64px;
        
        /* 移动端红点样式调整，使其水平居中 */
        .badge-dot {
          top: -1px;
          right: auto; /* 取消右对齐 */
          left: 50%; /* 让红点水平居中 */
          transform: translateX(-50%) scale(0.9); /* 添加水平居中的位移 */
        }
        
        &:last-child {
          height: 64px;
          min-width: 64px;  /* 确保足够空间显示Plus图标 */
        }
        
        .nav-icon {
          display: block; /* 在移动端显示图标 */
          height: 24px;
          display: flex;
          align-items: center;
          justify-content: center;
          
          svg {
            width: 22px;
            height: 22px;
            transition: color 0.3s ease;
          }
        }
        
        .nav-text {
          font-weight: 500;
          line-height: 1.2;
        }
        
        &.active {
          .nav-text {
            color: var(--theme-color);
          }
          .nav-icon svg {
            transform: scale(1); /* 覆盖桌面端的放大效果 */
          }
        }
      }
    }
  }
}

// 移动端底部导航样式 - 屏幕宽度小于768px时启用
@media (max-width: 768px) {
  .slide-tabs-container {
    top: auto;
    bottom: 20px;  /* 增加底部间距 - 从18px调整到20px, 向上移动2px */
    width: 92%;
    max-width: 450px;
    margin-bottom: 0;
    
    // 确保在移动设备上导航条可以充分展示
    .slide-tabs-wrapper {
      width: 100%;
      display: block;
      border-radius: 20px;
      padding: 3px;
    }
    
    .slide-tabs-nav {
      width: 100%;
      justify-content: space-around;
      
      .slider-indicator {
        // border-radius: 16px;
        // /* 确保指示器在移动设备上正确定位 */
        // top: 2px;
        // height: calc(100% - 4px);
        // background-color: rgba(var(--theme-color-rgb), 0.08);
        display: none; /* 移动设备上隐藏滑块指示器 */
      }
    }
  }
}

// 特小屏幕的响应式
@media (max-width: 480px) {
  .slide-tabs-container {
    bottom: 12px; /* 从10px调整到12px, 向上移动2px */
    width: 94%;
    
    .slide-tabs-wrapper {
      border-radius: 18px;
    }
    
    .slide-tabs-nav {
      .nav-item {
        padding: 5px 8px;
        font-size: 11px;
        height: 58px;
        
        .nav-icon {
          svg {
            width: 20px;
            height: 20px;
          }
        }
      }
    }
  }
}
</style> 