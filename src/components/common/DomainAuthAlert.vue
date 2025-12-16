<!--
  授权验证提示组件
  
  该组件在未授权时显示警告提示。
-->
<template>
  <div v-if="!isAuthorized" class="domain-auth-alert">
    <div class="alert-content">
      <div class="alert-icon">
        <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2C6.47715 2 2 6.47715 2 12C2 17.5228 6.47715 22 12 22Z" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
          <path d="M12 8V12" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
          <path d="M12 16H12.01" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </div>
      <div class="alert-message">
        <h3>{{ $t('auth.licenseNotAuthorized', 'N O L I C E N S E F A I L E D') }}</h3>
        <p>{{ $t('auth.contactAdmin', 'N O L I C E N S E F A I L E D') }}</p>
        <p class="current-domain">{{ apiDomain }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DomainAuthAlert',
  props: {
    isAuthorized: {
      type: Boolean,
      default: true
    },
    apiDomain: {
      type: String,
      default: ''
    }
  }
}
</script>

<style lang="scss" scoped>
.domain-auth-alert {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: rgba(var(--background-color-rgb), 0.9);
  backdrop-filter: blur(10px);
  z-index: 9999;
  padding: 20px;
  box-sizing: border-box;
}

.alert-content {
  max-width: 500px;
  padding: 30px;
  background-color: var(--card-background, #fff);
  border-radius: 10px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  
  .alert-icon {
    width: 80px;
    height: 80px;
    margin-bottom: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 50%;
    background-color: rgba(255, 56, 56, 0.1);
    color: #ff3838;
    
    svg {
      width: 40px;
      height: 40px;
    }
  }
  
  .alert-message {
    h3 {
      font-size: 24px;
      margin: 0 0 15px;
      color: var(--text-color, #333);
    }
    
    p {
      font-size: 16px;
      margin: 0 0 10px;
      color: var(--secondary-text-color, #666);
      
      &.current-domain {
        font-family: monospace;
        margin-top: 15px;
        padding: 10px;
        background-color: rgba(0, 0, 0, 0.05);
        border-radius: 5px;
        font-size: 14px;
        word-break: break-all;
      }
    }
  }
}

/* 暗色模式适配 */
:deep(.dark-theme) .domain-auth-alert {
  .alert-content {
    background-color: var(--card-background, #222);
    
    .alert-icon {
      background-color: rgba(255, 56, 56, 0.2);
    }
    
    .alert-message {
      h3 {
        color: var(--text-color, #eee);
      }
      
      p {
        color: var(--secondary-text-color, #aaa);
        
        &.current-domain {
          background-color: rgba(255, 255, 255, 0.1);
        }
      }
    }
  }
}
</style> 