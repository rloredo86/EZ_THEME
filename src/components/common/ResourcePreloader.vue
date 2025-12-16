<!-- 资源预加载组件 -->
<template>
  <!-- 这个组件不会显示任何内容，仅用于资源预加载 -->
  <div class="resource-preloader" style="display: none;">
    <!-- 用于预加载图片的隐藏容器 -->
    <div v-if="preloadImages.length > 0" style="display: none;">
      <img 
        v-for="(src, index) in preloadImages" 
        :key="index" 
        :src="src" 
        alt="preload-img"
        width="1"
        height="1"
        @load="onImageLoaded(src)"
      />
    </div>
  </div>
</template>

<script>
import { onMounted, ref, watch, computed } from 'vue';
import { useRoute } from 'vue-router';
import preloadManager from '@/utils/preloadManager';
import { CUSTOMER_SERVICE_CONFIG, AUTH_LAYOUT_CONFIG } from '@/utils/baseConfig';

export default {
  name: 'ResourcePreloader',
  setup() {
    const route = useRoute();
    const isPreloaded = ref({
      components: false,
      images: false,
      scripts: false
    });

    // 检查客服系统是否启用
    const isCustomerServiceEnabled = CUSTOMER_SERVICE_CONFIG && CUSTOMER_SERVICE_CONFIG.enabled;

    // 需要预加载的图片列表
    const preloadImages = ref([
      '/images/logo.png'
    ]);
    
    // 预加载队列和加载状态
    const preloadQueue = ref([]);
    const isLoading = ref(false);

    // 确定Auth布局类型，根据layoutType决定导入哪个版本的组件
    const authLayoutType = computed(() => {
      return AUTH_LAYOUT_CONFIG?.layoutType || 'center';
    });

    // 预加载客服页面组件
    const customerServiceComponent = { 
      path: 'CustomerService', 
      name: 'CustomerService', 
      priority: 0, // 最高优先级
      component: () => import('@/views/service/CustomerService.vue') 
    };

    // 需要预加载的路由组件列表和配置
    const componentsConfig = {
      // 基础组件（全局预加载）
      base: [
        { path: 'Dashboard', name: 'Dashboard', priority: 1, component: () => import('@/views/dashboard/Dashboard.vue') },
        { path: 'Shop', name: 'Shop', priority: 2, component: () => import('@/views/shop/Shop.vue') },
        { path: 'More', name: 'More', priority: 3, component: () => import('@/views/more/MoreOptions.vue') },
        { path: 'Invite', name: 'Invite', priority: 4, component: () => import('@/views/invite/Invite.vue') },
        { path: 'Profile', name: 'Profile', priority: 5, component: () => import('@/views/profile/UserProfile.vue') }
      ],
      
      // 路由特定组件
      route: {
        '/': [
          {
            path: 'Login',
            name: 'Login',
            priority: 1,
            component: () => authLayoutType.value === 'split' 
              ? import('@/views/auth/split/Login.vue')
              : import('@/views/auth/center/Login.vue')
          },
          {
            path: 'Register',
            name: 'Register',
            priority: 2,
            component: () => authLayoutType.value === 'split'
              ? import('@/views/auth/split/Register.vue')
              : import('@/views/auth/center/Register.vue')
          },
          {
            path: 'ForgotPassword',
            name: 'ForgotPassword',
            priority: 3,
            component: () => authLayoutType.value === 'split'
              ? import('@/views/auth/split/ForgotPassword.vue')
              : import('@/views/auth/center/ForgotPassword.vue')
          }
        ],
        '/landing': [
          {
            path: 'Login',
            name: 'Login',
            priority: 1,
            component: () => authLayoutType.value === 'split'
              ? import('@/views/auth/split/Login.vue')
              : import('@/views/auth/center/Login.vue')
          },
          {
            path: 'Register',
            name: 'Register',
            priority: 2,
            component: () => authLayoutType.value === 'split'
              ? import('@/views/auth/split/Register.vue')
              : import('@/views/auth/center/Register.vue')
          },
          {
            path: 'ForgotPassword',
            name: 'ForgotPassword',
            priority: 3,
            component: () => authLayoutType.value === 'split'
              ? import('@/views/auth/split/ForgotPassword.vue')
              : import('@/views/auth/center/ForgotPassword.vue')
          }
        ],
        '/login': [
          {
            path: 'Register',
            name: 'Register',
            priority: 1,
            component: () => authLayoutType.value === 'split'
              ? import('@/views/auth/split/Register.vue')
              : import('@/views/auth/center/Register.vue')
          },
          {
            path: 'ForgotPassword',
            name: 'ForgotPassword',
            priority: 2,
            component: () => authLayoutType.value === 'split'
              ? import('@/views/auth/split/ForgotPassword.vue')
              : import('@/views/auth/center/ForgotPassword.vue')
          },
          { path: 'Dashboard', name: 'Dashboard', priority: 3, component: () => import('@/views/dashboard/Dashboard.vue') }
        ],
        '/register': [
          {
            path: 'Login',
            name: 'Login',
            priority: 1,
            component: () => authLayoutType.value === 'split'
              ? import('@/views/auth/split/Login.vue')
              : import('@/views/auth/center/Login.vue')
          },
          {
            path: 'ForgotPassword',
            name: 'ForgotPassword',
            priority: 2,
            component: () => authLayoutType.value === 'split'
              ? import('@/views/auth/split/ForgotPassword.vue')
              : import('@/views/auth/center/ForgotPassword.vue')
          },
          { path: 'Dashboard', name: 'Dashboard', priority: 3, component: () => import('@/views/dashboard/Dashboard.vue') }
        ],
        '/forgot-password': [
          {
            path: 'Login',
            name: 'Login',
            priority: 1,
            component: () => authLayoutType.value === 'split'
              ? import('@/views/auth/split/Login.vue')
              : import('@/views/auth/center/Login.vue')
          },
          {
            path: 'Register',
            name: 'Register',
            priority: 2,
            component: () => authLayoutType.value === 'split'
              ? import('@/views/auth/split/Register.vue')
              : import('@/views/auth/center/Register.vue')
          },
          { path: 'Dashboard', name: 'Dashboard', priority: 3, component: () => import('@/views/dashboard/Dashboard.vue') }
        ],
        '/dashboard': [
          { path: 'Shop', name: 'Shop', priority: 1, component: () => import('@/views/shop/Shop.vue') },
          { path: 'More', name: 'More', priority: 2, component: () => import('@/views/more/MoreOptions.vue') },
          { path: 'Invite', name: 'Invite', priority: 3, component: () => import('@/views/invite/Invite.vue') },
          { path: 'Profile', name: 'Profile', priority: 4, component: () => import('@/views/profile/UserProfile.vue') },
          { path: 'OrderList', name: 'OrderList', priority: 5, component: () => import('@/views/orders/OrderList.vue') },
          { path: 'Payment', name: 'Payment', priority: 6, component: () => import('@/views/shop/Payment.vue') }
        ],
        '/shop': [
          { path: 'OrderConfirm', name: 'OrderConfirm', priority: 1, component: () => import('@/views/shop/OrderConfirm.vue') },
          { path: 'Payment', name: 'Payment', priority: 2, component: () => import('@/views/shop/Payment.vue') },
          { path: 'Dashboard', name: 'Dashboard', priority: 3, component: () => import('@/views/dashboard/Dashboard.vue') },
          { path: 'More', name: 'More', priority: 4, component: () => import('@/views/more/MoreOptions.vue') },
          { path: 'OrderList', name: 'OrderList', priority: 5, component: () => import('@/views/orders/OrderList.vue') }
        ],
        '/invite': [
          { path: 'Shop', name: 'Shop', priority: 1, component: () => import('@/views/shop/Shop.vue') },
          { path: 'More', name: 'More', priority: 2, component: () => import('@/views/more/MoreOptions.vue') },
          { path: 'Dashboard', name: 'Dashboard', priority: 3, component: () => import('@/views/dashboard/Dashboard.vue') }
        ],
        '/more': [
          { path: 'Profile', name: 'Profile', priority: 1, component: () => import('@/views/profile/UserProfile.vue') },
          { path: 'NodeList', name: 'NodeList', priority: 2, component: () => import('@/views/servers/NodeList.vue') },
          { path: 'OrderList', name: 'OrderList', priority: 3, component: () => import('@/views/orders/OrderList.vue') },
          { path: 'TicketList', name: 'TicketList', priority: 4, component: () => import('@/views/ticket/TicketList.vue') },
          { path: 'Docs', name: 'Docs', priority: 5, component: () => import('@/views/docs/DocsPage.vue') },
          { path: 'TrafficLog', name: 'TrafficLog', priority: 6, component: () => import('@/views/trafficLog/TrafficLog.vue') }
        ],
        '/profile': [
          { path: 'Dashboard', name: 'Dashboard', priority: 1, component: () => import('@/views/dashboard/Dashboard.vue') },
          { path: 'Shop', name: 'Shop', priority: 2, component: () => import('@/views/shop/Shop.vue') },
          { path: 'More', name: 'More', priority: 3, component: () => import('@/views/more/MoreOptions.vue') },
          { path: 'TicketList', name: 'TicketList', priority: 4, component: () => import('@/views/ticket/TicketList.vue') },
          { path: 'Profile', name: 'Profile', priority: 5, component: () => import('@/views/profile/UserProfile.vue') }
        ],
        '/tickets': [
          { path: 'Profile', name: 'Profile', priority: 1, component: () => import('@/views/profile/UserProfile.vue') },
          { path: 'Dashboard', name: 'Dashboard', priority: 2, component: () => import('@/views/dashboard/Dashboard.vue') },
          { path: 'MobileTickets', name: 'MobileTickets', priority: 3, component: () => import('@/views/ticket/MobileTicketList.vue') }
        ],
        '/mobile/tickets': [
          { path: 'TicketList', name: 'TicketList', priority: 1, component: () => import('@/views/ticket/TicketList.vue') },
          { path: 'Dashboard', name: 'Dashboard', priority: 2, component: () => import('@/views/dashboard/Dashboard.vue') }
        ],
        '/orders': [
          { path: 'Shop', name: 'Shop', priority: 1, component: () => import('@/views/shop/Shop.vue') },
          { path: 'Payment', name: 'Payment', priority: 2, component: () => import('@/views/shop/Payment.vue') },
          { path: 'Dashboard', name: 'Dashboard', priority: 3, component: () => import('@/views/dashboard/Dashboard.vue') }
        ],
        '/nodes': [
          { path: 'Dashboard', name: 'Dashboard', priority: 1, component: () => import('@/views/dashboard/Dashboard.vue') },
          { path: 'More', name: 'More', priority: 2, component: () => import('@/views/more/MoreOptions.vue') }
        ],
        '/docs': [
          { path: 'More', name: 'More', priority: 1, component: () => import('@/views/more/MoreOptions.vue') },
          { path: 'DocDetail', name: 'DocDetail', priority: 2, component: () => import('@/views/docs/DocDetail.vue') },
          { path: 'Dashboard', name: 'Dashboard', priority: 3, component: () => import('@/views/dashboard/Dashboard.vue') }
        ],
        '/docs/:id': [
          { path: 'Docs', name: 'Docs', priority: 1, component: () => import('@/views/docs/DocsPage.vue') },
          { path: 'More', name: 'More', priority: 2, component: () => import('@/views/more/MoreOptions.vue') }
        ],
        '/trafficlog': [
          { path: 'Dashboard', name: 'Dashboard', priority: 1, component: () => import('@/views/dashboard/Dashboard.vue') },
          { path: 'More', name: 'More', priority: 2, component: () => import('@/views/more/MoreOptions.vue') },
          { path: 'Profile', name: 'Profile', priority: 3, component: () => import('@/views/profile/UserProfile.vue') }
        ],
        '/wallet/deposit': [
          { path: 'Dashboard', name: 'Dashboard', priority: 1, component: () => import('@/views/dashboard/Dashboard.vue') },
          { path: 'Shop', name: 'Shop', priority: 2, component: () => import('@/views/shop/Shop.vue') },
          { path: 'Profile', name: 'Profile', priority: 3, component: () => import('@/views/profile/UserProfile.vue') }
        ],
        '/payment': [
          { path: 'OrderConfirm', name: 'OrderConfirm', priority: 1, component: () => import('@/views/shop/OrderConfirm.vue') },
          { path: 'Shop', name: 'Shop', priority: 2, component: () => import('@/views/shop/Shop.vue') },
          { path: 'Dashboard', name: 'Dashboard', priority: 3, component: () => import('@/views/dashboard/Dashboard.vue') }
        ],
        '/order-confirm': [
          { path: 'Shop', name: 'Shop', priority: 1, component: () => import('@/views/shop/Shop.vue') },
          { path: 'Payment', name: 'Payment', priority: 2, component: () => import('@/views/shop/Payment.vue') }
        ],
        '/customer-service': [
          { path: 'Dashboard', name: 'Dashboard', priority: 1, component: () => import('@/views/dashboard/Dashboard.vue') },
          { path: 'More', name: 'More', priority: 2, component: () => import('@/views/more/MoreOptions.vue') },
          { path: 'TicketList', name: 'TicketList', priority: 3, component: () => import('@/views/ticket/TicketList.vue') }
        ]
      }
    };

    // 图片加载完成处理
    const onImageLoaded = (src) => {
      preloadManager.markResourceLoaded(src);
    };

    // 预加载额外的JS或CSS资源
    const preloadExternalResources = () => {
      // 需要预加载的静态资源
      const resources = [
        // { href: '/css/main.css', as: 'style' },
        // { href: '/fonts/roboto.woff2', as: 'font', type: 'font/woff2' }
        // 添加其他需要预加载的资源
      ];

      resources.forEach(resource => {
        if (!preloadManager.isResourceLoaded(resource.href)) {
          const link = preloadManager.createPreloadLink(resource.href, resource.as, resource.type);
          if (link) {
            document.head.appendChild(link);
          }
        }
      });

      isPreloaded.value.scripts = true;
    };
    
    // 处理一个组件的加载
    const processNextComponent = () => {
      if (preloadQueue.value.length === 0) {
        isLoading.value = false;
        return;
      }
      
      isLoading.value = true;
      const nextComponent = preloadQueue.value.shift();
      
      if (preloadManager.isComponentLoaded(nextComponent.name)) {
        // 如果组件已加载，立即处理下一个
        // console.log(`组件 ${nextComponent.name} 已加载，跳过...`);
        processNextComponent();
        return;
      }
      
      // console.log(`开始加载组件: ${nextComponent.name}`);
      
      try {
        nextComponent.component()
          .then(() => {
            preloadManager.markComponentLoaded(nextComponent.name);
            // console.log(`组件 ${nextComponent.name} 加载完成`);
            
            // 延迟一点时间再加载下一个，避免连续加载影响性能
            setTimeout(() => {
              processNextComponent();
            }, 200);
          })
          .catch(() => {
            // console.error(`加载组件 ${nextComponent.name} 失败:`, err);
            // 即使失败也继续处理队列
            setTimeout(() => {
              processNextComponent();
            }, 200);
          });
      } catch (error) {
        // console.error(`预加载组件 ${nextComponent.name} 发生异常:`, error);
        // 发生异常也继续处理队列
        setTimeout(() => {
          processNextComponent();
        }, 200);
      }
    };
    
    // 将组件添加到预加载队列并开始处理
    const enqueueComponents = (components) => {
      if (!components || components.length === 0) return;
      
      // 按优先级排序
      const sortedComponents = [...components].sort((a, b) => a.priority - b.priority);
      
      // 过滤掉已加载的组件
      const newComponents = sortedComponents.filter(comp => !preloadManager.isComponentLoaded(comp.name));
      
      if (newComponents.length === 0) return;
      
      // 添加到队列
      preloadQueue.value.push(...newComponents);
      
      // 如果没有正在加载，开始处理队列
      if (!isLoading.value) {
        processNextComponent();
      }
    };

    // 预加载Vue组件 - 使用队列方式一个一个加载
    const preloadVueComponents = () => {
      if (isPreloaded.value.components) return;
      
      // console.log('预加载组件资源开始，使用队列模式...');
      
      // 开始计时
      preloadManager.startPreloadTimer();
      
      // 如果客服系统已启用，则先预加载客服页面组件
      if (isCustomerServiceEnabled) {
        enqueueComponents([customerServiceComponent]);
      }
      
      // 延迟加载基础组件
      setTimeout(() => {
        // 如果客服系统已启用，给每个基础配置的路由都添加客服组件
        if (isCustomerServiceEnabled) {
          componentsConfig.base.unshift(customerServiceComponent);
        }
        enqueueComponents(componentsConfig.base);
      }, 3000);
      
      // 延迟加载当前路由相关组件
      setTimeout(() => {
        if (route.path in componentsConfig.route) {
          // 如果客服系统已启用，给当前路由也添加客服组件
          if (isCustomerServiceEnabled && route.path !== '/customer-service') {
            componentsConfig.route[route.path].unshift(customerServiceComponent);
          }
          enqueueComponents(componentsConfig.route[route.path]);
        }
      }, 1500);
      
      isPreloaded.value.components = true;
      
      // 6秒后输出统计信息
      setTimeout(() => {
        // console.log('预加载组件统计:', preloadManager.getPreloadStats());
      }, 8000);
    };

    // 主预加载处理函数
    const preloadResources = () => {
      // 使用requestIdleCallback或setTimeout避免影响初始渲染
      const schedulePreload = (callback, timeout) => {
        if (window.requestIdleCallback) {
          window.requestIdleCallback(callback, { timeout });
        } else {
          setTimeout(callback, timeout / 2);
        }
      };

      // 按顺序预加载不同类型的资源，避免同时进行过多加载导致性能问题
      schedulePreload(() => {
        preloadExternalResources();
      }, 2000);

      schedulePreload(() => {
        preloadVueComponents();
      }, 3000);

      // 图片通常较小，可以较早预加载
      isPreloaded.value.images = true;
    };

    // 路由变化时更新预加载策略
    watch(() => route.path, (newPath, oldPath) => {
      if (newPath !== oldPath && newPath in componentsConfig.route) {
        // console.log(`路由变化: ${oldPath} -> ${newPath}, 预加载新路由组件...`);
        
        // 立即开始预加载新路由相关组件
        // 如果客服系统已启用且不是客服页面，给新路由添加客服组件
        if (isCustomerServiceEnabled && newPath !== '/customer-service' && !componentsConfig.route[newPath].some(comp => comp.name === 'CustomerService')) {
          componentsConfig.route[newPath].unshift(customerServiceComponent);
        }
        enqueueComponents(componentsConfig.route[newPath]);
      }
    });

    onMounted(() => {
      // 在页面首次渲染完成后触发预加载
      // 这里使用更长的延迟，确保不干扰首屏渲染和初始动画
      setTimeout(() => {
        preloadResources();
      }, 2000);
    });

    return {
      isPreloaded,
      preloadImages,
      onImageLoaded
    };
  }
};
</script> 