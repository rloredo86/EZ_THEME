<template>
  <div 
    v-if="isVisible" 
    class="customer-service-icon"
    :class="{ 'is-mobile': isMobile }"
    :style="positionStyle"
    @click="navigateToService"
  >
    <IconHeadset :size="isMobile ? 24 : 28" />
  </div>
</template>

<script>
import { computed, onMounted, ref } from 'vue';
import { useRouter } from 'vue-router';
import { useStore } from 'vuex';
import { CUSTOMER_SERVICE_CONFIG } from '@/utils/baseConfig';
import { IconHeadset } from '@tabler/icons-vue';

export default {
  name: 'CustomerServiceIcon',
  components: {
    IconHeadset
  },
  setup() {
    const router = useRouter();
    const store = useStore();
    const isMobile = ref(false);
    
    // 检测是否为移动设备
    const checkIfMobile = () => {
      isMobile.value = window.innerWidth < 768;
    };
    
    // 初始检测
    onMounted(() => {
      checkIfMobile();
      window.addEventListener('resize', checkIfMobile);
    });
    
    // 用户是否已登录
    const isLoggedIn = computed(() => store.getters.isLoggedIn);
    
    // 判断是否应该显示客服图标
    const shouldShow = computed(() => {
      // 如果客服系统未启用，则不显示
      if (!CUSTOMER_SERVICE_CONFIG.enabled) return false;
      
      // 如果是嵌入模式，不显示图标
      if (CUSTOMER_SERVICE_CONFIG.embedMode === 'embed' && CUSTOMER_SERVICE_CONFIG.type === 'crisp') {
        return false;
      }
      
      // 如果配置为未登录时不显示且用户未登录，则不显示
      if (!CUSTOMER_SERVICE_CONFIG.showWhenNotLoggedIn && !isLoggedIn.value) {
        return false;
      }
      
      return true;
    });
    
    // 计算图标位置样式
    const positionStyle = computed(() => {
      if (isMobile.value) {
        return {
          right: CUSTOMER_SERVICE_CONFIG.iconPosition.mobile.right,
          bottom: CUSTOMER_SERVICE_CONFIG.iconPosition.mobile.bottom,
          left: 'auto'
        };
      } else {
        return {
          left: CUSTOMER_SERVICE_CONFIG.iconPosition.desktop.left,
          bottom: CUSTOMER_SERVICE_CONFIG.iconPosition.desktop.bottom,
          right: 'auto'
        };
      }
    });
    
    // 导航到客服页面
    const navigateToService = () => {
      router.push('/customer-service');
    };
    
    return {
      isMobile,
      isVisible: shouldShow,
      positionStyle,
      navigateToService
    };
  }
};
</script>

<style lang="scss" scoped>
.customer-service-icon {
  position: fixed;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 48px;
  height: 48px;
  border-radius: 50%;
  background-color: var(--theme-color);
  color: white;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
  cursor: pointer;
  z-index: 99;
  transition: all 0.3s ease;
  
  &:hover {
    transform: scale(1.1);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.25);
  }
  
  &.is-mobile {
    width: 42px;
    height: 42px;
  }
}
</style> 