<template>
  <div class="ios-modal-overlay">
    <div class="ios-modal">
      <div class="ios-modal-content">
        <div class="ios-modal-title">提醒</div>
        <div class="ios-modal-text">我社正发生大规模人事变动，具体人事详情尚未统计完毕，故该页面暂时不进行展示。</div>
        <div class="ios-modal-actions">
          <button class="ios-btn primary" @click="goBack">我知道了</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const goBack = () => {
  if (window.history.length > 1) {
    window.history.back()
  } else {
    window.location.href = '/'
  }
}
</script>

<style scoped>
.ios-modal-overlay{position:fixed;inset:0;background:rgba(0,0,0,.35);backdrop-filter:blur(8px);-webkit-backdrop-filter:blur(8px);display:flex;align-items:center;justify-content:center;z-index:10002}
.ios-modal{max-width:400px;border-radius:24px;background:var(--vp-c-bg-soft);box-shadow:0 20px 40px rgba(0,0,0,.35);border:1px solid var(--vp-c-divider);padding:24px 24px 28px;text-align:left}
.ios-modal-content{width:320px;max-width:80vw;margin:0 auto}
.ios-modal-title{font-size:18px;font-weight:700;color:var(--vp-c-text-1);margin-bottom:12px}
.ios-modal-text{font-size:15px;color:var(--vp-c-text-1);line-height:1.7;margin:0 0 14px 0}
.ios-modal-actions{display:flex;gap:10px;justify-content:center}
.ios-btn{border:none;border-radius:22px;height:44px;padding:0 18px;font-size:16px;font-weight:700;width:100%}
.ios-btn.primary{background:linear-gradient(135deg,var(--vp-c-brand),var(--vp-c-brand-2));color:#fff;box-shadow:0 6px 14px rgba(0,0,0,.2)}
</style>