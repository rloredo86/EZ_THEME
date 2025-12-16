<template>
  <transition-group tag="div" name="toast-list" class="toast-container">
    <div
      v-for="toast in toasts"
      :key="toast.id"
      class="toast"
      :class="[`toast-${toast.type}`, { 'toast-show': toast.show }]"
      :data-id="toast.id"
      @click="removeToast(toast.id)"
      @mouseenter="pauseToastTimer(toast.id)"
      @mouseleave="resumeToastTimer(toast.id)"
    >
      <div class="toast-icon">
        <svg class="icon" :class="getIconClass(toast.type)">
          <use :xlink:href="getIconPath(toast.type)"></use>
        </svg>
      </div>
      <div class="toast-content">
        {{ toast.message }}
      </div>
      <div class="toast-close">
        <button type="button" class="close-btn" @click.stop="removeToast(toast.id)">
          &times;
        </button>
      </div>
      <div class="toast-progress-bar" :data-id="toast.id" :style="{ animationDuration: `${toast.duration}ms` }"></div>
    </div>
  </transition-group>
</template>

<script>
import { useToast } from '@/composables/useToast';

export default {
  name: 'ToastNotification',
  setup() {
    const { toasts, removeToast, pauseToastTimer, resumeToastTimer } = useToast();
    
    const getIconClass = (type) => {
      return `icon-${type}`;
    };
    
    const getIconPath = (type) => {
      switch (type) {
        case 'success':
          return '#icon-check-circle';
        case 'error':
          return '#icon-error-circle';
        case 'warning':
          return '#icon-warning';
        default:
          return '#icon-info-circle';
      }
    };
    
    return {
      toasts,
      removeToast,
      pauseToastTimer,
      resumeToastTimer,
      getIconClass,
      getIconPath
    };
  }
};
</script>

<style lang="scss" scoped>
.toast-container {
  position: fixed;
  top: 20px;
  right: 20px;
  z-index: 9999;
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  gap: 12px;
  pointer-events: none;
  width: 400px;
  
  @media (max-width: 576px) {
    top: 0;
    left: 0;
    right: 0;
    padding: 16px;
    width: 100%;
    align-items: stretch;
  }
}

.toast {
  min-width: 300px;
  max-width: 380px;
  background-color: var(--card-background);
  color: var(--text-color);
  border-radius: 12px;
  padding: 14px 16px;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.12);
  display: flex;
  align-items: center;
  gap: 12px;
  opacity: 0;
  transform: translateX(100%);
  transition: all 0.4s cubic-bezier(0.16, 1, 0.3, 1);
  pointer-events: auto;
  cursor: pointer;
  overflow: hidden;
  position: relative;
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  
  @media (max-width: 576px) {
    min-width: 0;
    width: 100%;
    max-width: none;
    transform: translateY(-100%);
    border-radius: 10px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
    
    &.toast-show {
      transform: translateY(0);
    }
    
    &:hover {
      transform: translateY(0);
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
    }
  }
  
  &.toast-show {
    opacity: 1;
    transform: translateX(0);
  }
  
  &:hover {
    transform: translateY(-2px) scale(1.02);
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.15);
    
    .toast-progress-bar {
      animation-play-state: paused;
    }
  }
  
  &.toast-success {
    border-left: 4px solid var(--success-color);
    
    .toast-icon {
      color: var(--success-color);
    }
    
    .toast-progress-bar {
      background-color: var(--success-color);
    }
  }
  
  &.toast-error {
    border-left: 4px solid var(--error-color);
    
    .toast-icon {
      color: var(--error-color);
    }
    
    .toast-progress-bar {
      background-color: var(--error-color);
    }
  }
  
  &.toast-warning {
    border-left: 4px solid var(--warning-color);
    
    .toast-icon {
      color: var(--warning-color);
    }
    
    .toast-progress-bar {
      background-color: var(--warning-color);
    }
  }
  
  &.toast-info {
    border-left: 4px solid var(--info-color);
    
    .toast-icon {
      color: var(--info-color);
    }
    
    .toast-progress-bar {
      background-color: var(--info-color);
    }
  }
}

.toast-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.25rem;
  
  .icon {
    width: 24px;
    height: 24px;
    stroke-width: 2;
    stroke-linecap: round;
    stroke-linejoin: round;
    fill: none;
    
    &.icon-success {
      stroke: var(--success-color);
    }
    
    &.icon-error {
      stroke: var(--error-color);
    }
    
    &.icon-warning {
      stroke: var(--warning-color);
    }
    
    &.icon-info {
      stroke: var(--info-color);
    }
  }
}

.toast-content {
  flex: 1;
  font-size: 0.95rem;
  font-weight: 500;
}

.toast-close {
  display: flex;
  align-items: center;
  
  .close-btn {
    background: none;
    border: none;
    font-size: 1.25rem;
    line-height: 1;
    color: var(--secondary-text-color);
    cursor: pointer;
    padding: 0;
    opacity: 0.6;
    transition: opacity 0.2s ease;
    
    &:hover {
      opacity: 1;
    }
  }
}

.toast-progress-bar {
  position: absolute;
  bottom: 0;
  left: 0;
  height: 3px;
  width: 100%;
  transform-origin: left;
  animation: progress-bar-shrink 3000ms linear forwards;
  animation-play-state: running;
  opacity: 0.8;
  will-change: transform;
}

@keyframes progress-bar-shrink {
  0% {
    transform: scaleX(1);
  }
  100% {
    transform: scaleX(0);
  }
}

.toast-list-enter-active {
  transition: all 0.4s cubic-bezier(0.16, 1, 0.3, 1);
  transition-delay: 0.1s;
}

.toast-list-leave-active {
  transition: all 0.4s cubic-bezier(0.6, 0.04, 0.98, 0.335);
  position: absolute;
  
  @media (max-width: 576px) {
    width: calc(100% - 32px);
    left: 16px;
    right: 16px;
  }
}

.toast-list-enter-from {
  opacity: 0;
  transform: translateX(100%);
  
  @media (max-width: 576px) {
    transform: translateY(-100%);
  }
}

.toast-list-leave-to {
  opacity: 0;
  transform: translateX(100%);
  
  @media (max-width: 576px) {
    transform: translateY(-100%);
  }
}
</style> 