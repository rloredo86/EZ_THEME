<template>
  <div class="user-avatar-container" ref="avatarContainer">
    <div class="avatar-wrapper" @click="toggleDropdown">
      <img 
        v-if="avatarUrl" 
        :src="avatarUrl" 
        alt="User Avatar" 
        class="avatar-image"
      />
      <div v-else class="avatar-placeholder">
        <IconUser class="user-icon" />
      </div>
    </div>
    
    <transition name="fade">
      <div 
        class="dropdown-menu" 
        v-if="isDropdownOpen"
      >
        <div class="menu-item" @click="navigateTo('/profile')">
          <IconUser class="menu-icon" />
          <span>{{ $t('common.userCenter') }}</span>
        </div>
        <div class="menu-item" v-if="isXiaoV2board" @click="navigateTo('/wallet/deposit')">
          <IconWallet class="menu-icon" />
          <span>{{ $t('common.myWallet') }}</span>
        </div>
        <div class="menu-item" @click="navigateTo('/profile?openPasswordModal=true')">
          <IconLock class="menu-icon" />
          <span>{{ $t('common.changePassword') }}</span>
        </div>
        <div class="divider"></div>
        <div class="menu-item" @click="logout">
          <IconLogout class="menu-icon" />
          <span>{{ $t('common.logoutText') }}</span>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import { ref, onMounted, onUnmounted } from 'vue';
import { useRouter } from 'vue-router';
import { useI18n } from 'vue-i18n';
import { useToast } from '@/composables/useToast';
import { isXiaoV2board } from '@/utils/baseConfig';
import IconUser from '@/components/icons/IconUser.vue';
import IconLogout from '@/components/icons/IconLogout.vue';
import IconWallet from '@/components/icons/IconWallet.vue';
import IconLock from '@/components/icons/IconLock.vue';

export default {
  name: 'UserAvatar',
  components: {
    IconUser,
    IconLogout,
    IconWallet,
    IconLock
  },
  props: {
    username: {
      type: String,
      default: ''
    },
    avatarUrl: {
      type: String,
      default: ''
    }
  },
  setup() {
    const router = useRouter();
    const { t } = useI18n();
    const { showToast } = useToast();
    const isDropdownOpen = ref(false);
    const avatarContainer = ref(null);
    
    // 切换下拉菜单显示状态
    const toggleDropdown = () => {
      isDropdownOpen.value = !isDropdownOpen.value;
    };
    
    // 导航到指定路径
    const navigateTo = (path) => {
      isDropdownOpen.value = false;
      router.push(path);
    };
    
    // 退出登录
    const logout = async () => {
      try {
        // 这里应该调用退出登录的 action
        // await store.dispatch('auth/logout');
        localStorage.removeItem('token'); // 简单实现，删除token
        isDropdownOpen.value = false;
        
        // 显示退出成功消息
        showToast(t('auth.logoutSuccess'), 'success', 3000);
        
        // 延迟跳转，确保消息能够显示
        setTimeout(() => {
          router.push('/login');
        }, 500);
      } catch (error) {
        console.error('退出登录失败:', error);
        showToast(t('auth.logoutFailed'), 'error');
      }
    };
    
    // 点击外部关闭下拉菜单
    const handleClickOutside = (event) => {
      if (avatarContainer.value && !avatarContainer.value.contains(event.target)) {
        isDropdownOpen.value = false;
      }
    };
    
    onMounted(() => {
      document.addEventListener('click', handleClickOutside);
    });
    
    onUnmounted(() => {
      document.removeEventListener('click', handleClickOutside);
    });
    
    return {
      isDropdownOpen,
      toggleDropdown,
      navigateTo,
      logout,
      avatarContainer,
      isXiaoV2board: isXiaoV2board()
    };
  }
};
</script>

<style lang="scss" scoped>
.user-avatar-container {
  position: relative;
}

.avatar-wrapper {
  width: 38px;
  height: 38px;
  border-radius: 50%;
  cursor: pointer;
  overflow: hidden;
  background-color: rgba(var(--theme-color-rgb), 0.1);
  border: 1px solid rgba(var(--theme-color-rgb), 0.3);
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  
  &:hover {
    box-shadow: 0 0 0 3px rgba(var(--theme-color-rgb), 0.15);
    transform: translateY(-2px);
  }
  
  .avatar-image {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
  
  .avatar-placeholder {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 100%;
    
    .user-icon {
      width: 20px;
      height: 20px;
      color: var(--theme-color);
    }
  }
}

.dropdown-menu {
  position: absolute;
  top: calc(100% + 8px);
  right: 0;
  width: 180px;
  background: rgba(var(--card-background-rgb), 0.9);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border-radius: 12px;
  box-shadow: 0 4px 20px var(--shadow-color);
  border: 1px solid var(--border-color);
  overflow: hidden;
  z-index: 100;
  animation: dropdownFadeIn 0.2s ease;
  
  .menu-item {
    display: flex;
    align-items: center;
    padding: 12px 16px;
    cursor: pointer;
    transition: all 0.3s ease;
    
    .menu-icon {
      width: 18px;
      height: 18px;
      margin-right: 10px;
      color: var(--text-color);
      transition: color 0.3s ease;
    }
    
    span {
      font-size: 14px;
      color: var(--text-color);
      transition: color 0.3s ease;
    }
    
    &:hover {
      background-color: rgba(var(--primary-color-rgb), 0.1);
      color: var(--primary-color);
      
      .menu-icon, span {
        color: var(--primary-color);
      }
    }
    
    &:last-child {
      .menu-icon, span {
        transition: color 0.5s ease;
      }
      
      &:hover {
        background-color: rgba(245, 108, 108, 0.05);
        transition: background-color 0.5s ease;
        
        .menu-icon, span {
          color: #f56c6c;
          transition: color 0.5s ease;
        }
      }
    }
  }
  
  .divider {
    height: 1px;
    background-color: var(--border-color);
    margin: 4px 0;
  }
}

// 淡入淡出动画
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease, transform 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}

@keyframes dropdownFadeIn {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style> 