<template>
  <div ref="rootEl">
    <Transition name="notice">
      <div v-if="modalOpen" class="ios-modal-overlay">
        <div class="ios-modal">
          <div class="ios-modal-content">
            <div class="ios-modal-title">提醒</div>
            <div class="ios-modal-text">组织内近期发生变动较大，此页面的信息可能已过期或不再正确。</div>
            <div class="ios-modal-actions">
              <button class="ios-btn primary" @click="confirm">我知道了</button>
            </div>
          </div>
        </div>
      </div>
    </Transition>
    <div v-if="confirmed" class="bottom-pill" @click="openFromPill">组织内近期发生变动较大，此页面的信息可能已过期或不再正确。</div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, nextTick, watch } from 'vue'
const confirmed = ref(false)
const modalOpen = ref(false)
const rootEl = ref<HTMLElement | null>(null)
onMounted(() => {
  try {
    confirmed.value = localStorage.getItem('noticeConfirmed') === '1'
  } catch {}
  modalOpen.value = !confirmed.value
  applyChinese()
})
const confirm = () => {
  confirmed.value = true
  modalOpen.value = false
  try {
    localStorage.setItem('noticeConfirmed', '1')
  } catch {}
}
const openFromPill = () => {
  modalOpen.value = true
  nextTick(() => applyChinese())
}
function applyChinese(){
  try{
    const pref = localStorage.getItem('preferredChinese')
    const isTraditional = pref === 'traditional'
    const OpenCC = (window as any).OpenCC
    if(OpenCC && rootEl.value){
      const to = isTraditional ? OpenCC.Converter({ from:'cn', to:'twp'}) : OpenCC.Converter({ from:'twp', to:'cn'})
      const walker = document.createTreeWalker(rootEl.value, NodeFilter.SHOW_TEXT)
      const nodes: Text[] = []
      let cur = walker.nextNode()
      while(cur){
        const txt = (cur as Text).nodeValue || ''
        if(/[\u4e00-\u9fa5]/.test(txt)) nodes.push(cur as Text)
        cur = walker.nextNode()
      }
      nodes.forEach(n=>{ n.nodeValue = to((n.nodeValue)||'') })
    }
  }catch{}
}
watch([modalOpen, confirmed], async ()=>{ await nextTick(); applyChinese() })
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
.bottom-pill{position:fixed;right:20px;bottom:20px;background:var(--vp-c-bg-soft);color:var(--vp-c-text-1);border:1px solid var(--vp-c-divider);border-radius:9999px;padding:10px 16px;font-size:13px;box-shadow:var(--vp-shadow-2);z-index:10001;max-width:60vw;cursor:pointer}
@media (max-width:768px){.bottom-pill{left:12px;right:12px;bottom:14px;max-width:none;text-align:center}}
.notice-enter-active,.notice-leave-active{transition:opacity .16s ease}
.notice-enter-from,.notice-leave-to{opacity:0}
.notice-enter-from .ios-modal{transform:scale(.985) translateY(6px);opacity:0}
.notice-enter-to .ios-modal{transform:scale(1) translateY(0);opacity:1;transition:transform .18s ease,opacity .18s ease}
.notice-leave-from .ios-modal{transform:scale(1) translateY(0);opacity:1}
.notice-leave-to .ios-modal{transform:scale(.99) translateY(5px);opacity:0;transition:transform .16s ease,opacity .16s ease}
</style>
