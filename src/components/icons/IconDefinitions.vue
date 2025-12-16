<template>
  <svg
    xmlns="http://www.w3.org/2000/svg"
    style="position: absolute; width: 0; height: 0; overflow: hidden"
    version="1.1"
  >
    <defs>
      <!-- 成功图标 -->
      <symbol id="icon-check-circle" viewBox="0 0 24 24">
        <circle cx="12" cy="12" r="10"></circle>
        <path d="M9 12l2 2 4-4"></path>
      </symbol>
      
      <!-- 错误图标 -->
      <symbol id="icon-error-circle" viewBox="0 0 24 24">
        <circle cx="12" cy="12" r="10"></circle>
        <line x1="15" y1="9" x2="9" y2="15"></line>
        <line x1="9" y1="9" x2="15" y2="15"></line>
      </symbol>
      
      <!-- 警告图标 -->
      <symbol id="icon-warning" viewBox="0 0 24 24">
        <path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"></path>
        <line x1="12" y1="9" x2="12" y2="13"></line>
        <line x1="12" y1="17" x2="12.01" y2="17"></line>
      </symbol>
      
      <!-- 信息图标 -->
      <symbol id="icon-info-circle" viewBox="0 0 24 24">
        <circle cx="12" cy="12" r="10"></circle>
        <line x1="12" y1="16" x2="12" y2="12"></line>
        <line x1="12" y1="8" x2="12.01" y2="8"></line>
      </symbol>
    </defs>
  </svg>
</template>

<script>
export default {
  name: 'IconDefinitions'
};
</script> 