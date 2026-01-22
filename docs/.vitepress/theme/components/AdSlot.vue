<template>
  <div class="ad-container">
    <div class="ad-support-notice">
      本站内容免费。页面可能展示广告，用于覆盖服务器与维护成本。
    </div>
    <ins class="adsbygoogle"
         style="display:block"
         data-ad-client="ca-pub-1238777311285568"
         data-ad-slot="8138079461"
         data-ad-format="auto"
         data-full-width-responsive="true"></ins>
  </div>
</template>

<script setup>
import { onMounted } from 'vue'

onMounted(() => {
  // 确保 adsbygoogle 已加载
  if (typeof window !== 'undefined') {
    try {
      (window.adsbygoogle = window.adsbygoogle || []).push({})
    } catch (e) {
      console.error('AdSense error:', e)
    }
  }
})
</script>

<style scoped>
.ad-container {
  margin: 2rem 0;
  padding: 1rem 0;
  border-top: 1px solid var(--vp-c-divider);
}

.ad-support-notice {
  font-size: 0.85rem;
  color: var(--vp-c-text-3);
  text-align: center;
  margin-bottom: 1rem;
}
</style>
