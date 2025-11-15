<template>
  <div class="mobile-menu-overlay" :class="{ open: isOpen }" @click="closeMenu">
    <div class="mobile-menu-container" @click.stop>
      <div class="mobile-menu-header">
        <div class="menu-title-container">
          <img :src="logoSrc" alt="星轨社" class="menu-logo" width="24" height="24" />
          <h3 class="menu-title">{{ siteTitle }}</h3>
        </div>
        <button class="close-button" @click="closeMenu" aria-label="关闭菜单">
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" width="24" height="24">
            <line x1="18" y1="6" x2="6" y2="18"></line>
            <line x1="6" y1="6" x2="18" y2="18"></line>
          </svg>
        </button>
      </div>
      
      <!-- 工具栏区域 -->
      <div class="mobile-menu-toolbar">
        <div class="toolbar-item" @click="toggleLocale">
          <svg class="toolbar-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" width="20" height="20">
            <circle cx="12" cy="12" r="10"></circle>
            <path d="M2 12h20"></path>
            <path d="M12 2v20"></path>
          </svg>
          <span class="toolbar-text">{{ isEnglish ? '中文' : 'English' }}</span>
        </div>
        
        <div class="toolbar-item" @click="toggleTheme">
          <svg class="toolbar-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" width="20" height="20">
            <circle cx="12" cy="12" r="5"></circle>
            <line x1="12" y1="1" x2="12" y2="3"></line>
            <line x1="12" y1="21" x2="12" y2="23"></line>
            <line x1="4.22" y1="4.22" x2="5.64" y2="5.64"></line>
            <line x1="18.36" y1="18.36" x2="19.78" y2="19.78"></line>
            <line x1="1" y1="12" x2="3" y2="12"></line>
            <line x1="21" y1="12" x2="23" y2="12"></line>
            <line x1="4.22" y1="19.78" x2="5.64" y2="18.36"></line>
            <line x1="18.36" y1="5.64" x2="19.78" y2="4.22"></line>
          </svg>
          <span class="toolbar-text">{{ isDark ? '暗' : '亮' }}</span>
        </div>
        
        <div class="toolbar-item" @click="toggleLanguage">
          <svg class="toolbar-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" width="20" height="20">
            <path d="M5 12h14"></path>
            <path d="M12 5v14"></path>
            <path d="M16.5 8.5L22 3"></path>
            <path d="M2 21l5.5-5.5"></path>
            <path d="M16.5 15.5L22 21"></path>
            <path d="M2 3l5.5 5.5"></path>
          </svg>
          <span class="toolbar-text">{{ isTraditional ? '繁體' : '简体' }}</span>
        </div>
        
        <div class="toolbar-item" @click="openSearch">
          <svg class="toolbar-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" width="20" height="20">
            <circle cx="11" cy="11" r="8"></circle>
            <path d="m21 21-4.3-4.3"></path>
          </svg>
          <span class="toolbar-text">搜索</span>
        </div>
      </div>
      
      <nav class="mobile-nav">
        <div class="nav-items">
          <div v-for="(item, idx) in navigationItems" :key="item.text" class="nav-item">
            <!-- 普通链接 -->
            <a v-if="!item.items" :href="item.link" class="nav-link" @click="closeMenu">
              <span class="nav-icon pop" :style="{ animationDelay: `${idx * 40}ms` }" v-html="getIcon(item.text)"></span>
              <span class="nav-text">{{ item.text }}</span>
            </a>
            
            <!-- 下拉菜单 -->
            <div v-else class="nav-dropdown">
              <button class="nav-dropdown-toggle" @click="toggleDropdown(item.text)">
                <span class="nav-icon pop" :style="{ animationDelay: `${idx * 40}ms` }" v-html="getIcon(item.text)"></span>
                <span class="nav-text">{{ item.text }}</span>
                <span class="dropdown-arrow" :class="{ open: openDropdowns.includes(item.text) }">
                  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" width="16" height="16">
                    <polyline points="6 9 12 15 18 9"></polyline>
                  </svg>
                </span>
              </button>
              
              <div class="nav-dropdown-items" v-show="openDropdowns.includes(item.text)">
                <a v-for="subItem in item.items" :key="subItem.text" :href="subItem.link" 
                   class="nav-dropdown-item" @click="closeMenu">
                  <span class="nav-text">{{ subItem.text }}</span>
                </a>
              </div>
            </div>
          </div>
        </div>
      </nav>
      
      <div class="mobile-menu-footer">
        <div class="social-links">
          <a v-for="link in socialLinks" :key="link.link" :href="link.link" class="social-link" target="_blank" rel="noopener noreferrer">
            <span class="social-icon" aria-hidden="true" v-html="getSocialIcon(link.icon)"></span>
          </a>
        </div>
        <div class="menu-info">
          <p class="version">{{ versionText }}</p>
          <p class="copyright">星轨旅行奇想社</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, inject } from 'vue'
import { useData } from 'vitepress'
import versionConf from '../../version.json'

interface NavItem {
  text: string
  link?: string
  items?: NavItem[]
}

const props = defineProps<{
  isOpen: boolean
}>()

const emit = defineEmits<{
  close: []
}>()

const openDropdowns = ref<string[]>([])
const { theme } = useData()
const { lang } = useData()
const { site } = useData()
const logoSrc = computed(() => (theme.value?.logo as any) || '/head&favicon.png')
const isEnglish = computed(() => (lang.value || 'zh-CN').startsWith('en'))
const siteTitle = computed(() => (site.value?.title as any) || '星轨旅行奇想社')
const versionText = (versionConf as any).version || 'V0.0.0.DEV'

// 获取主题切换函数
const toggleAppearance = inject<() => void>('toggle-appearance', () => {})

// 获取当前主题状态
const isDark = ref(false)
const isTraditional = ref(false)

// 监听主题变化
onMounted(() => {
  // 获取当前主题
  const updateTheme = () => {
    isDark.value = document.documentElement.classList.contains('dark')
  }
  
  // 获取当前语言
  const updateLanguage = () => {
    const html = document.documentElement
    const preferred = localStorage.getItem('preferredChinese')
    isTraditional.value = html.classList.contains('zh-traditional') || preferred === 'traditional'
  }
  
  updateTheme()
  updateLanguage()
  
  // 监听主题变化
  const observer = new MutationObserver(() => {
    updateTheme()
    updateLanguage()
  })
  
  observer.observe(document.documentElement, {
    attributes: true,
    attributeFilter: ['class', 'lang', 'data-lang']
  })
  
  // 初始检查
  setTimeout(() => {
    updateTheme()
    updateLanguage()
  }, 100)
})

// 导航数据 - 读取自 config.mts (themeConfig.nav)，且过滤版本项
const navigationItems = computed<NavItem[]>(() => {
  const nav = (theme.value?.nav ?? []) as any[]
  return nav.filter((item: any) => {
    const txt = String(item.text || '')
    return !/^V\d/.test(txt)
  })
})

// 社交链接 - 读取自 config.mts (themeConfig.socialLinks)
const socialLinks = computed<any[]>(() => (theme.value?.socialLinks ?? []))

const getSocialIcon = (icon: any): string => {
  if (!icon) return ''
  if (typeof icon === 'string') return ''
  if (icon.svg) return icon.svg
  return ''
}

const iconMap: Record<string, string> = {
  '主页': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m3 9 9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path><polyline points="9 22 9 12 15 12 15 22"></polyline></svg>',
  '导航': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polygon points="3 11 22 2 13 21 11 13 3 11"></polygon></svg>',
  '加入聊天室': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"></path></svg>',
  '购买': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="9" cy="21" r="1"></circle><circle cx="20" cy="21" r="1"></circle><path d="M1 1h4l2.68 13.39a2 2 0 0 0 2 1.61h9.72a2 2 0 0 0 2-1.61L23 6H6"></path></svg>',
  '其他': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="1"></circle><circle cx="12" cy="5" r="1"></circle><circle cx="12" cy="19" r="1"></circle></svg>',
  '部门页面': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M16 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"></path><circle cx="8.5" cy="7" r="4"></circle><path d="M20 8v6"></path><path d="M23 11h-6"></path></svg>',
  'V6.1.207.DEV': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle><line x1="12" y1="8" x2="12" y2="12"></line><line x1="12" y1="16" x2="12.01" y2="16"></line></svg>',
  '招贤纳士': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M16 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"></path><circle cx="8.5" cy="7" r="4"></circle><line x1="18" y1="8" x2="23" y2="13"></line><line x1="23" y1="8" x2="18" y2="13"></line></svg>'
}

const getIcon = (text: string): string => {
  const direct = iconMap[text]
  if (direct) return direct
  const t = text.toLowerCase()
  if (t.includes('home')) return iconMap['主页']
  if (t.includes('nav')) return iconMap['导航']
  if (t.includes('community') || t.includes('chat')) return iconMap['加入聊天室']
  if (t.includes('buy') || t.includes('purchase')) return iconMap['购买']
  if (t.includes('other')) return iconMap['其他']
  if (t.includes('department')) return iconMap['部门页面']
  if (t.includes('join')) return iconMap['招贤纳士']
  return ''
}

const toggleDropdown = (text: string) => {
  const index = openDropdowns.value.indexOf(text)
  if (index > -1) {
    openDropdowns.value.splice(index, 1)
  } else {
    openDropdowns.value.push(text)
  }
}

const closeMenu = () => {
  emit('close')
  openDropdowns.value = []
}

// 工具栏功能
const toggleTheme = () => {
  // 直接切换主题
  if (typeof document !== 'undefined') {
    const html = document.documentElement
    const isCurrentlyDark = html.classList.contains('dark')
    
    if (isCurrentlyDark) {
      html.classList.remove('dark')
      localStorage.setItem('vitepress-theme-appearance', 'light')
    } else {
      html.classList.add('dark')
      localStorage.setItem('vitepress-theme-appearance', 'dark')
    }
    
    // 更新本地状态
    isDark.value = !isCurrentlyDark
  }
}

const toggleLanguage = () => {
  const btn = document.querySelector('.chinese-converter') as HTMLButtonElement
  const html = document.documentElement
  const w = window as any
  const perform = () => {
    if (btn) {
      btn.click()
    } else if (w.OpenCC && typeof w.OpenCC.convert === 'function') {
      let preferred = localStorage.getItem('preferredChinese') || (html.classList.contains('zh-traditional') ? 'traditional' : 'simplified')
      if (preferred === 'simplified') {
        w.OpenCC.convert('traditional')
        preferred = 'traditional'
        html.classList.add('zh-traditional')
      } else {
        w.OpenCC.convert('simplified')
        preferred = 'simplified'
        html.classList.remove('zh-traditional')
      }
      localStorage.setItem('preferredChinese', preferred)
    }
    setTimeout(() => {
      const preferred = localStorage.getItem('preferredChinese')
      isTraditional.value = html.classList.contains('zh-traditional') || preferred === 'traditional'
    }, 30)
  }
  html.classList.add('chinese-converting')
  perform()
  requestAnimationFrame(() => {
    setTimeout(() => html.classList.remove('chinese-converting'), 120)
  })
}

const toggleLocale = () => {
  const loc = location
  const path = loc.pathname
  const toEnglish = !path.startsWith('/en/')
  let newPath
  if (toEnglish) {
    newPath = path.startsWith('/') ? `/en${path}` : `/en/${path}`
  } else {
    newPath = path.replace(/^\/en\//, '/').replace(/^\/en$/, '/')
  }
  const url = `${newPath}${loc.search}${loc.hash}`
  loc.assign(url)
}

const openSearch = () => {
  // 触发搜索功能
  const searchButton = document.querySelector('.DocSearch-Button, .VPNavBarSearchButton') as HTMLButtonElement
  if (searchButton) {
    searchButton.click()
  } else {
    // 如果找不到搜索按钮，尝试使用键盘快捷键
    const event = new KeyboardEvent('keydown', {
      key: 'k',
      ctrlKey: true,
      bubbles: true
    })
    document.dispatchEvent(event)
  }
  closeMenu() // 打开搜索后关闭菜单
}

// 监听ESC键
onMounted(() => {
  const handleEscape = (e: KeyboardEvent) => {
    if (e.key === 'Escape' && props.isOpen) {
      closeMenu()
    }
  }
  
  document.addEventListener('keydown', handleEscape)
  
  return () => {
    document.removeEventListener('keydown', handleEscape)
  }
})
</script>

<style scoped>
.mobile-menu-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  z-index: 10002;
  opacity: 0;
  visibility: hidden;
  transition: opacity 0.3s cubic-bezier(0.4, 0, 0.2, 1), visibility 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.mobile-menu-overlay.open {
  opacity: 1;
  visibility: visible;
}

.mobile-menu-container {
  position: fixed;
  top: 0;
  right: 0;
  width: 320px;
  max-width: 90vw;
  height: 100vh;
  background: var(--vp-c-bg);
  border-left: 1px solid var(--vp-c-divider);
  transform: translateX(100%);
  transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.mobile-menu-overlay.open .mobile-menu-container {
  transform: translateX(0);
}

@keyframes slideInHeader {
  from { opacity: 0; transform: translateX(16px); }
  to { opacity: 1; transform: translateX(0); }
}

.mobile-menu-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 1.25rem 1.5rem;
  border-bottom: 1px solid var(--vp-c-divider);
  background: var(--vp-c-bg-soft);
  flex-shrink: 0;
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
  animation: slideInHeader 0.35s cubic-bezier(0.4, 0, 0.2, 1) both;
}

.mobile-menu-toolbar {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  align-items: center;
  justify-items: center;
  gap: 0.5rem;
  padding: 1rem 1.5rem;
  border-bottom: 1px solid var(--vp-c-divider);
  background: var(--vp-c-bg-soft);
  flex-shrink: 0;
}

.toolbar-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.375rem;
  padding: 0.5rem;
  border-radius: 0.5rem;
  cursor: pointer;
  transition: all 0.2s ease;
  min-width: 0;
  flex: 1;
}

.toolbar-item:hover {
  background: var(--vp-c-bg-mute);
  transform: translateY(-1px);
}

.toolbar-item:active {
  transform: translateY(0);
}

.toolbar-icon {
  color: var(--vp-c-text-2);
  transition: color 0.2s ease;
}

.toolbar-item:hover .toolbar-icon {
  color: var(--vp-c-brand);
}

.toolbar-text {
  font-size: 0.75rem;
  color: var(--vp-c-text-2);
  font-weight: 500;
  transition: color 0.2s ease;
  text-align: center;
}

.menu-title-container {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.menu-logo {
  color: var(--vp-c-brand);
  opacity: 0;
  transform: translateX(12px);
  transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.menu-title {
  margin: 0;
  font-size: 1.125rem;
  font-weight: 600;
  color: var(--vp-c-text-1);
  letter-spacing: -0.025em;
  opacity: 0;
  transform: translateX(12px);
  transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.mobile-menu-overlay.open .menu-logo,
.mobile-menu-overlay.open .menu-title {
  opacity: 1;
  transform: translateX(0);
}

.close-button {
  background: none;
  border: none;
  padding: 0.5rem;
  border-radius: 0.5rem;
  color: var(--vp-c-text-2);
  cursor: pointer;
  transition: all 0.2s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  outline: none;
}

.close-button:hover {
  background: var(--vp-c-bg-mute);
  color: var(--vp-c-text-1);
  transform: scale(1.1);
}

.close-button:active {
  transform: scale(0.95);
}

.close-button:focus-visible {
  box-shadow: 0 0 0 2px var(--vp-c-brand);
}

.mobile-nav {
  flex: 1;
  overflow-y: auto;
  padding: 1rem 0;
}

.nav-items {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

.nav-item {
  padding: 0 1rem;
}

.nav-link {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.875rem 1rem;
  border-radius: 0.75rem;
  color: var(--vp-c-text-1);
  text-decoration: none;
  transition: all 0.2s ease;
  font-weight: 500;
  position: relative;
  overflow: hidden;
  width: 100%;
  box-sizing: border-box;
}

.nav-link:hover {
  background: var(--vp-c-bg-soft);
  color: var(--vp-c-brand);
  transform: translateX(4px);
}

.nav-link:active {
  transform: translateX(2px);
}

.nav-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 1.25rem;
  height: 1.25rem;
  flex-shrink: 0;
  opacity: 0.7;
  transition: opacity 0.2s ease;
}

.pop {
  transform-origin: center;
}

.mobile-menu-overlay.open .pop {
  animation: popIn 140ms cubic-bezier(0.22, 1, 0.36, 1) both;
}

@keyframes popIn {
  0% { transform: scale(0.85); }
  100% { transform: scale(1); }
}

.nav-link:hover .nav-icon {
  opacity: 1;
}

.nav-icon :deep(svg) {
  width: 100%;
  height: 100%;
  stroke: currentColor;
  fill: none;
}

.nav-dropdown {
  display: flex;
  flex-direction: column;
}

.nav-dropdown-toggle {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  width: 100%;
  padding: 0.875rem 1rem;
  border: none;
  background: none;
  color: var(--vp-c-text-1);
  font-weight: 500;
  cursor: pointer;
  border-radius: 0.75rem;
  transition: all 0.2s ease;
  position: relative;
  overflow: hidden;
  box-sizing: border-box;
}

.nav-dropdown-toggle:hover {
  background: var(--vp-c-bg-soft);
  color: var(--vp-c-brand);
  transform: translateX(4px);
}

.nav-dropdown-toggle:active {
  transform: translateX(2px);
}

.dropdown-arrow {
  margin-left: auto;
  transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0.6;
}

.dropdown-arrow.open {
  transform: rotate(180deg);
}

.dropdown-arrow :deep(svg) {
  stroke: currentColor;
  fill: none;
}

.nav-dropdown-items {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
  margin-top: 0.5rem;
  padding-left: 2rem;
  animation: slideDown 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.nav-dropdown-item {
  display: block;
  padding: 0.625rem 0.875rem;
  border-radius: 0.5rem;
  color: var(--vp-c-text-2);
  text-decoration: none;
  transition: all 0.2s ease;
  font-size: 0.875rem;
  font-weight: 400;
  position: relative;
}

.nav-dropdown-item:hover {
  background: var(--vp-c-bg-soft);
  color: var(--vp-c-brand);
  transform: translateX(4px);
}

.nav-dropdown-item:active {
  transform: translateX(2px);
}

.mobile-menu-footer {
  border-top: 1px solid var(--vp-c-divider);
  padding: 1.25rem 1.5rem;
  background: var(--vp-c-bg-soft);
  flex-shrink: 0;
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
}

.social-links {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  justify-content: center;
  margin-bottom: 0.75rem;
}

.social-link {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  border-radius: 50%;
  color: var(--vp-c-text-2);
  transition: all 0.2s ease;
  line-height: 0;
  overflow: hidden;
  border: 1px solid var(--vp-c-divider);
  background: var(--vp-c-bg);
}

.social-link:hover {
  color: var(--vp-c-brand);
  transform: translateY(-1px);
}

.social-icon {
  display: flex;
  width: 18px;
  height: 18px;
  align-items: center;
  justify-content: center;
}

:deep(.social-links) svg {
  width: 18px;
  height: 18px;
  display: block;
  margin: 0 auto;
}

.menu-info {
  text-align: center;
}

.version {
  margin: 0 0 0.375rem 0;
  font-size: 0.8125rem;
  color: var(--vp-c-text-2);
  font-weight: 500;
  font-family: ui-monospace, SFMono-Regular, 'SF Mono', Menlo, Monaco, Consolas, 'Liberation Mono', 'Courier New', monospace;
}

.copyright {
  margin: 0;
  font-size: 0.8125rem;
  color: var(--vp-c-text-3);
  font-weight: 400;
}

@keyframes slideDown {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: scale(0.95);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

/* 添加微妙的背景渐变效果 */
.mobile-menu-container {
  background: linear-gradient(135deg, var(--vp-c-bg) 0%, var(--vp-c-bg-soft) 100%);
}

/* 添加微妙的阴影效果 */
.mobile-menu-overlay.open .mobile-menu-container {
  box-shadow: -10px 0 40px rgba(0, 0, 0, 0.1);
}

:deep(.dark) .mobile-menu-overlay.open .mobile-menu-container {
  box-shadow: -10px 0 40px rgba(0, 0, 0, 0.3);
}

/* 添加微妙的悬停发光效果 */
.nav-link:hover::before,
.nav-dropdown-toggle:hover::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: linear-gradient(90deg, transparent, var(--vp-c-brand) 50%, transparent);
  opacity: 0.05;
  pointer-events: none;
  border-radius: inherit;
}

:deep(.dark) .nav-link:hover::before,
:deep(.dark) .nav-dropdown-toggle:hover::before {
  opacity: 0.1;
}

/* 暗色主题适配 */
:deep(.dark) .mobile-menu-container {
  background: linear-gradient(135deg, var(--vp-c-bg) 0%, var(--vp-c-bg-soft) 100%);
  border-left-color: var(--vp-c-divider);
}

:deep(.dark) .mobile-menu-header {
  background: var(--vp-c-bg-soft);
  border-bottom-color: var(--vp-c-divider);
}

:deep(.dark) .mobile-menu-footer {
  background: var(--vp-c-bg-soft);
  border-top-color: var(--vp-c-divider);
}

:deep(.dark) .close-button:hover {
  background: var(--vp-c-bg-mute);
}

:deep(.dark) .nav-link:hover {
  background: var(--vp-c-bg-soft);
}

:deep(.dark) .nav-dropdown-toggle:hover {
  background: var(--vp-c-bg-soft);
}

:deep(.dark) .nav-dropdown-item:hover {
  background: var(--vp-c-bg-soft);
}

/* 暗色主题下的悬停发光效果 */
:deep(.dark) .nav-link:hover::before,
:deep(.dark) .nav-dropdown-toggle:hover::before {
  background: linear-gradient(90deg, transparent, var(--vp-c-brand) 50%, transparent);
  opacity: 0.1;
}

/* 暗色主题下的工具栏样式 */
:deep(.dark) .mobile-menu-toolbar {
  background: var(--vp-c-bg-soft);
  border-bottom-color: var(--vp-c-divider);
}

:deep(.dark) .toolbar-item:hover {
  background: var(--vp-c-bg-mute);
}

:deep(.dark) .toolbar-icon {
  color: var(--vp-c-text-2);
}

:deep(.dark) .toolbar-item:hover .toolbar-icon {
  color: var(--vp-c-brand);
}

:deep(.dark) .toolbar-text {
  color: var(--vp-c-text-2);
}

:deep(.dark) .toolbar-item:hover .toolbar-text {
  color: var(--vp-c-brand);
}

/* 移动端适配 */
@media (max-width: 768px) {
  .mobile-menu-container {
    width: 100vw;
    max-width: 100vw;
  }
}

/* 高对比度模式 */
@media (prefers-contrast: high) {
  .mobile-menu-overlay {
    background: rgba(0, 0, 0, 0.8);
  }
  
  .nav-link,
  .nav-dropdown-toggle {
    border: 1px solid transparent;
  }
  
  .nav-link:hover,
  .nav-dropdown-toggle:hover {
    border-color: var(--vp-c-brand);
  }
}

/* 添加微妙的进入动画 */
.mobile-menu-overlay {
  will-change: opacity, visibility;
}

.mobile-menu-container {
  will-change: transform;
}

/* 添加菜单项的渐入动画 */
.mobile-menu-header,
.mobile-menu-toolbar,
.mobile-nav,
.mobile-menu-footer {
  animation: fadeIn 0.4s cubic-bezier(0.4, 0, 0.2, 1) forwards;
}

.mobile-menu-toolbar {
  animation-delay: 0.05s;
}

.mobile-nav {
  animation-delay: 0.1s;
}

.mobile-menu-footer {
  animation-delay: 0.15s;
}

/* 添加微妙的图标动画 */
.nav-icon {
  transition: all 0.2s ease;
}

.nav-link:hover .nav-icon,
.nav-dropdown-toggle:hover .nav-icon {
  transform: scale(1.1);
}

/* 添加微妙的文本阴影效果 */
.menu-title {
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
}

:deep(.dark) .menu-title {
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
}

/* 减少动画偏好 */
@media (prefers-reduced-motion: reduce) {
  .mobile-menu-overlay,
  .mobile-menu-container,
  .dropdown-arrow,
  .nav-dropdown-items,
  .mobile-menu-header,
  .mobile-menu-toolbar,
  .mobile-nav,
  .mobile-menu-footer {
    transition: none;
    animation: none;
  }
}
</style>