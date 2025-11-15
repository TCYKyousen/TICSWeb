<script setup lang="ts">
import { useData } from 'vitepress'
import DefaultTheme from 'vitepress/theme'
import { nextTick, provide, onMounted, ref } from 'vue'
import ChineseConverter from '@theme/components/ChineseConverter.vue'
import NoticeManager from '@theme/NoticeManager.vue'
import MobileMenu from '@theme/components/MobileMenu.vue'

const { isDark } = useData()

// 移动端菜单状态
const isMobileMenuOpen = ref(false)

const openMobileMenu = () => {
  isMobileMenuOpen.value = true
  document.body.style.overflow = 'hidden'
  // 添加active类到自定义汉堡按钮
  const customTrigger = document.querySelector('.custom-mobile-menu-trigger')
  if (customTrigger) {
    customTrigger.classList.add('active')
  }
  document.documentElement.classList.add('mobile-menu-open')
}

const closeMobileMenu = () => {
  isMobileMenuOpen.value = false
  document.body.style.overflow = ''
  // 移除active类从自定义汉堡按钮
  const customTrigger = document.querySelector('.custom-mobile-menu-trigger')
  if (customTrigger) {
    customTrigger.classList.remove('active')
  }
  document.documentElement.classList.remove('mobile-menu-open')
}

const enableTransitions = () =>
  'startViewTransition' in document &&
  window.matchMedia('(prefers-reduced-motion: no-preference)').matches

provide('toggle-appearance', async ({ clientX: x, clientY: y }: MouseEvent) => {
  if (!enableTransitions()) {
    isDark.value = !isDark.value
    return
  }

  const clipPath = [
    `circle(0px at ${x}px ${y}px)`,
    `circle(${Math.hypot(
      Math.max(x, innerWidth - x),
      Math.max(y, innerHeight - y)
    )}px at ${x}px ${y}px)`
  ]

  await document.startViewTransition(async () => {
    isDark.value = !isDark.value
    await nextTick()
  }).ready

  document.documentElement.animate(
    { clipPath: isDark.value ? clipPath.reverse() : clipPath },
    {
      duration: 300,
      easing: 'ease-in',
      pseudoElement: `::view-transition-${isDark.value ? 'old' : 'new'}(root)`
    }
  )
})

onMounted(() => {
  const hidePcNavbarItems = () => {
    const selectors = [
      '.VPNavBarMenu',
      '.VPNavBarMenuLink',
      '.VPNavBarMenuGroup',
      '.VPNavBarMenuGroupContent',
      '.VPNavBarNav',
      '.VPNavBarExtra',
      '.VPNavBarSearch',
      '.VPSocialLinks',
      '.VPNavBarAppearance',
      '.VPNavBarLocaleMenu'
    ]
    selectors.forEach(sel => {
      document.querySelectorAll(sel).forEach(el => {
        (el as HTMLElement).style.display = 'none'
      })
    })
  }
  hidePcNavbarItems()
  const obs = new MutationObserver(() => hidePcNavbarItems())
  obs.observe(document.body, { childList: true, subtree: true })
})

const iconMap: Record<string, string> = {
  '主页': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M10 20v-6h4v6h5v-8h3L12 3 2 12h3v8z"/></svg>',
  '主頁': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M10 20v-6h4v6h5v-8h3L12 3 2 12h3v8z"/></svg>',
  'home': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M10 20v-6h4v6h5v-8h3L12 3 2 12h3v8z"/></svg>',

  '导航': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M12 2C8.13 2 5 5.13 5 9c0 5.25 7 13 7 13s7-7.75 7-13c0-3.87-3.13-7-7-7zm0 9.5c-1.38 0-2.5-1.12-2.5-2.5s1.12-2.5 2.5-2.5 2.5 1.12 2.5 2.5-1.12 2.5-2.5 2.5z"/></svg>',
  '導航': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M12 2C8.13 2 5 5.13 5 9c0 5.25 7 13 7 13s7-7.75 7-13c0-3.87-3.13-7-7-7zm0 9.5c-1.38 0-2.5-1.12-2.5-2.5s1.12-2.5 2.5-2.5 2.5 1.12 2.5 2.5-1.12 2.5-2.5 2.5z"/></svg>',
  '導覽': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M12 2C8.13 2 5 5.13 5 9c0 5.25 7 13 7 13s7-7.75 7-13c0-3.87-3.13-7-7-7zm0 9.5c-1.38 0-2.5-1.12-2.5-2.5s1.12-2.5 2.5-2.5 2.5 1.12 2.5 2.5-1.12 2.5-2.5 2.5z"/></svg>',
  'nav': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M12 2C8.13 2 5 5.13 5 9c0 5.25 7 13 7 13s7-7.75 7-13c0-3.87-3.13-7-7-7zm0 9.5c-1.38 0-2.5-1.12-2.5-2.5s1.12-2.5 2.5-2.5 2.5 1.12 2.5 2.5-1.12 2.5-2.5 2.5z"/></svg>',

  '加入聊天室': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M20 2H4c-1.1 0-2 .9-2 2v18l4-4h14c1.1 0 2-.9 2-2V4c0-1.1-.9-2-2-2zm0 14H6l-2 2V4h16v12z"/></svg>',
  'community': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M20 2H4c-1.1 0-2 .9-2 2v18l4-4h14c1.1 0 2-.9 2-2V4c0-1.1-.9-2-2-2zm0 14H6l-2 2V4h16v12z"/></svg>',

  '购买': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M7 18c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2zM1 2v2h2l3.6 7.59-1.35 2.45c-.15.28-.25.61-.25.96 0 1.1.9 2 2 2h12v-2H7.42c-.14 0-.25-.11-.25-.25l.03-.12L8.1 13h7.45c.75 0 1.41-.41 1.75-1.03L21.7 4H5.21l-.94-2H1zm16 16c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2z"/></svg>',
  '購買': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M7 18c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2zM1 2v2h2l3.6 7.59-1.35 2.45c-.15.28-.25.61-.25.96 0 1.1.9 2 2 2h12v-2H7.42c-.14 0-.25-.11-.25-.25l.03-.12L8.1 13h7.45c.75 0 1.41-.41 1.75-1.03L21.7 4H5.21l-.94-2H1zm16 16c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2z"/></svg>',
  '购买/客服': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M7 18c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2zM1 2v2h2l3.6 7.59-1.35 2.45c-.15.28-.25.61-.25.96 0 1.1.9 2 2 2h12v-2H7.42c-.14 0-.25-.11-.25-.25l.03-.12L8.1 13h7.45c.75 0 1.41-.41 1.75-1.03L21.7 4H5.21l-.94-2H1zm16 16c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2z"/></svg>',
  '購買/客服': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M7 18c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2zM1 2v2h2l3.6 7.59-1.35 2.45c-.15.28-.25.61-.25.96 0 1.1.9 2 2 2h12v-2H7.42c-.14 0-.25-.11-.25-.25l.03-.12L8.1 13h7.45c.75 0 1.41-.41 1.75-1.03L21.7 4H5.21l-.94-2H1zm16 16c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2z"/></svg>',
  '購買／客服': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M7 18c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2zM1 2v2h2l3.6 7.59-1.35 2.45c-.15.28-.25.61-.25.96 0 1.1.9 2 2 2h12v-2H7.42c-.14 0-.25-.11-.25-.25l.03-.12L8.1 13h7.45c.75 0 1.41-.41 1.75-1.03L21.7 4H5.21l-.94-2H1zm16 16c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2z"/></svg>',

  '订单号说明': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M14 2H6c-1.1 0-1.99.9-1.99 2L4 20c0 1.1.89 2 1.99 2H18c1.1 0 2-.9 2-2V8l-6-6zm2 16H8v-2h8v2zm0-4H8v-2h8v2zm-3-5V3.5L18.5 9H13z"/></svg>',
  '訂單號說明': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M14 2H6c-1.1 0-1.99.9-1.99 2L4 20c0 1.1.89 2 1.99 2H18c1.1 0 2-.9 2-2V8l-6-6zm2 16H8v-2h8v2zm0-4H8v-2h8v2zm-3-5V3.5L18.5 9H13z"/></svg>',

  '其他': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M12 8c1.1 0 2-.9 2-2s-.9-2-2-2-2 .9-2 2 .9 2 2 2zm0 2c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2zm0 6c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2z"/></svg>',
  'others': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M12 8c1.1 0 2-.9 2-2s-.9-2-2-2-2 .9-2 2 .9 2 2 2zm0 2c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2zm0 6c-1.1 0-2 .9-2 2s.9 2 2 2 2-.9 2-2-.9-2-2-2z"/></svg>',

  'E.U.L.A': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M14 2H6c-1.1 0-1.99.9-1.99 2L4 20c0 1.1.89 2 1.99 2H18c1.1 0 2-.9 2-2V8l-6-6zm2 16H8v-2h8v2zm0-4H8v-2h8v2zm-3-5V3.5L18.5 9H13z"/></svg>',
  'e.u.l.a': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M14 2H6c-1.1 0-1.99.9-1.99 2L4 20c0 1.1.89 2 1.99 2H18c1.1 0 2-.9 2-2V8l-6-6zm2 16H8v-2h8v2zm0-4H8v-2h8v2zm-3-5V3.5L18.5 9H13z"/></svg>',

  '部门页面': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M12 7V3H2v18h20V7H12zM6 19H4v-2h2v2zm0-4H4v-2h2v2zm0-4H4V9h2v2zm0-4H4V5h2v2zm4 12H8v-2h2v2zm0-4H8v-2h2v2zm0-4H8V9h2v2zm0-4H8V5h2v2zm10 12h-8v-2h2v-2h-2v-2h2v-2h-2V9h8v10zm-2-8h-2v2h2v-2zm0 4h-2v2h2v-2z"/></svg>',
  '部門頁面': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M12 7V3H2v18h20V7H12zM6 19H4v-2h2v2zm0-4H4v-2h2v2zm0-4H4V9h2v2zm0-4H4V5h2v2zm4 12H8v-2h2v2zm0-4H8v-2h2v2zm0-4H8V9h2v2zm0-4H8V5h2v2zm10 12h-8v-2h2v-2h-2v-2h2v-2h-2V9h8v10zm-2-8h-2v2h2v-2zm0 4h-2v2h2v-2z"/></svg>',

  '历史研究院': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M4 6H2v14c0 1.1.9 2 2 2h14v-2H4V6zm16-4H8c-1.1 0-2 .9-2 2v12c0 1.1.9 2 2 2h12c1.1 0 2-.9 2-2V4c0-1.1-.9-2-2-2zm-1 9h-4v4h-2v-4H9V9h4V5h2v4h4v2z"/></svg>',
  '歷史研究院': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M4 6H2v14c0 1.1.9 2 2 2h14v-2H4V6zm16-4H8c-1.1 0-2 .9-2 2v12c0 1.1.9 2 2 2h12c1.1 0 2-.9 2-2V4c0-1.1-.9-2-2-2zm-1 9h-4v4h-2v-4H9V9h4V5h2v4h4v2z"/></svg>',
  'history institute': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M4 6H2v14c0 1.1.9 2 2 2h14v-2H4V6zm16-4H8c-1.1 0-2 .9-2 2v12c0 1.1.9 2 2 2h12c1.1 0 2-.9 2-2V4c0-1.1-.9-2-2-2zm-1 9h-4v4h-2v-4H9V9h4V5h2v4h4v2z"/></svg>',

  'V6.0.0': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm1 15h-2v-2h2v2zm0-4h-2V7h2v6z"/></svg>',

  '招贤纳士': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M16 11c1.66 0 2.99-1.34 2.99-3S17.66 5 16 5c-1.66 0-3 1.34-3 3s1.34 3 3 3zm-8 0c1.66 0 2.99-1.34 2.99-3S9.66 5 8 5C6.34 5 5 6.34 5 8s1.34 3 3 3zm0 2c-2.33 0-7 1.17-7 3.5V19h14v-2.5c0-2.33-4.67-3.5-7-3.5zm8 0c-.29 0-.62.02-.97.05 1.16.84 1.97 1.97 1.97 3.45V19h6v-2.5c0-2.33-4.67-3.5-7-3.5z"/></svg>',
  '招賢納士': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M16 11c1.66 0 2.99-1.34 2.99-3S17.66 5 16 5c-1.66 0-3 1.34-3 3s1.34 3 3 3zm-8 0c1.66 0 2.99-1.34 2.99-3S9.66 5 8 5C6.34 5 5 6.34 5 8s1.34 3 3 3zm0 2c-2.33 0-7 1.17-7 3.5V19h14v-2.5c0-2.33-4.67-3.5-7-3.5zm8 0c-.29 0-.62.02-.97.05 1.16.84 1.97 1.97 1.97 3.45V19h6v-2.5c0-2.33-4.67-3.5-7-3.5z"/></svg>',
  'join': '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path d="M16 11c1.66 0 2.99-1.34 2.99-3S17.66 5 16 5c-1.66 0-3 1.34-3 3s1.34 3 3 3zm-8 0c1.66 0 2.99-1.34 2.99-3S9.66 5 8 5C6.34 5 5 6.34 5 8s1.34 3 3 3zm0 2c-2.33 0-7 1.17-7 3.5V19h14v-2.5c0-2.33-4.67-3.5-7-3.5zm8 0c-.29 0-.62.02-.97.05 1.16.84 1.97 1.97 1.97 3.45V19h6v-2.5c0-2.33-4.67-3.5-7-3.5z"/></svg>'
}

// 调试：输出图标映射
if (typeof window !== 'undefined') {
  (window as any).__iconMap = iconMap
}

// 添加导航图标
const addNavIcons = () => {
  let added = false

  // 处理所有可能的导航链接选择器
  const selectors = [
    '.VPNavBarMenuLink',
    'nav .VPNavBarMenuLink',
    '.VPNavBarMenu .VPNavBarMenuLink',
    'a[class*="NavBarMenuLink"]'
  ]

  selectors.forEach(selector => {
    const navLinks = document.querySelectorAll(selector)
    navLinks.forEach((item: Element) => {
      // 跳过已有图标的项
      if ((item as HTMLElement).querySelector('.nav-icon-svg')) {
        return
      }

      // 尝试多种方式获取文本
      let textElement: Element | null = null
      let text = ''

      // 方法1: 查找 .VPText
      textElement = item.querySelector('.VPText')
      if (textElement) {
        text = textElement.textContent?.trim() || ''
      }

      // 方法2: 直接获取链接文本
      if (!text) {
        text = (item as HTMLElement).textContent?.trim() || ''
        textElement = item
      }

      // 方法3: 查找span
      if (!textElement || !text) {
        const span = item.querySelector('span')
        if (span) {
          textElement = span
          text = span.textContent?.trim() || ''
        }
      }

      // 查找匹配的图标
      const lower = text.toLowerCase()
      const iconSvg = iconMap[text] || iconMap[lower]
      
      if (text && iconSvg && textElement) {
        try {
          // 创建图标容器
          const iconWrapper = document.createElement('span')
          iconWrapper.className = 'nav-icon-svg'
          iconWrapper.innerHTML = iconSvg
          iconWrapper.style.cssText = 'display: inline-flex; align-items: center; vertical-align: middle; margin-right: 6px; width: 18px; height: 18px;'
          
          // 设置SVG样式
          const svg = iconWrapper.querySelector('svg')
          if (svg) {
            svg.style.cssText = 'width: 100%; height: 100%; fill: currentColor;'
          }
          
          // 清理文本节点（移除可能存在的空白）
          const firstChild = textElement.firstChild
          if (firstChild && firstChild.nodeType === Node.TEXT_NODE) {
            const textNode = firstChild as Text
            textNode.textContent = textNode.textContent?.trimStart() || ''
          }
          
          // 尝试在文本元素前插入
          if (textElement.firstChild) {
            textElement.insertBefore(iconWrapper, textElement.firstChild)
          } else {
            textElement.appendChild(iconWrapper)
          }
          
          
          added = true
        } catch (e) {
          console.warn('[NavIcons] Failed to add icon for', text, ':', e)
        }
      }
    })
  })
  
  // 处理下拉菜单项
  const dropdownSelectors = [
    '.VPNavBarMenuDropdownItem .VPNavBarMenuLink',
    '.VPNavBarMenuDropdownItem a',
    '.VPNavBarMenuGroupContent .VPNavBarMenuLink',
    '.VPNavBarMenuGroupContent a',
    '[class*="MenuDropdown"] .VPNavBarMenuLink',
    '[class*="MenuDropdown"] a',
    '.VPNavBarMenuGroup .VPNavBarMenuLink'
  ]

  dropdownSelectors.forEach(selector => {
    const dropdownItems = document.querySelectorAll(selector)
    dropdownItems.forEach((item: Element) => {
      // 跳过已经有图标的项
      if ((item as HTMLElement).querySelector('.nav-icon-svg')) {
        return
      }

      // 尝试多种方式获取文本元素
      let textElement: Element | null = null
      let text = ''

      // 方法1: 查找 .VPText
      textElement = item.querySelector('.VPText')
      if (textElement) {
        text = textElement.textContent?.trim() || ''
      }

      // 方法2: 查找直接包含文本的span
      if (!text || !textElement) {
        const spans = item.querySelectorAll('span')
        for (const span of Array.from(spans)) {
          const spanText = span.textContent?.trim() || ''
          // 跳过图标容器
          if (span.classList.contains('nav-icon-svg')) continue
          // 如果这个span有文本且不是空的
          if (spanText && iconMap[spanText]) {
            text = spanText
            textElement = span
            break
          }
        }
      }

      // 方法3: 直接获取链接的文本
      if (!text || !textElement) {
        text = (item as HTMLElement).textContent?.trim() || ''
        textElement = item
      }

      const lower = text.toLowerCase()
      const iconSvg = iconMap[text] || iconMap[lower]
      
      // 调试信息
      if (text && !iconSvg) {
        console.log('[NavIcons] No icon found for dropdown text:', text)
      }
      
      if (text && iconSvg && textElement) {
        try {
          // 创建图标容器
          const iconWrapper = document.createElement('span')
          iconWrapper.className = 'nav-icon-svg'
          iconWrapper.innerHTML = iconSvg
          iconWrapper.style.cssText = 'display: inline-flex; align-items: center; vertical-align: middle; margin-right: 0; width: 16px; height: 16px; flex-shrink: 0;'
          
          // 设置SVG样式
          const svg = iconWrapper.querySelector('svg')
          if (svg) {
            svg.style.cssText = 'width: 100%; height: 100%; fill: currentColor;'
          }
          
          // 清理文本节点的空白
          if (textElement.firstChild && textElement.firstChild.nodeType === Node.TEXT_NODE) {
            const textNode = textElement.firstChild as Text
            textNode.textContent = textNode.textContent?.trimStart() || ''
          }
          
          // 插入图标
          if (textElement.firstChild) {
            textElement.insertBefore(iconWrapper, textElement.firstChild)
          } else {
            textElement.appendChild(iconWrapper)
          }
          
          
          console.log('[NavIcons] Added icon for dropdown item:', text)
          added = true
        } catch (e) {
          console.warn('[NavIcons] Failed to add dropdown icon for', text, ':', e)
        }
      }
    })
  })

  return added
}

onMounted(() => {
  let retryCount = 0
  const maxRetries = 100

  // 替换原生移动端菜单为自定义菜单
  const replaceNativeMobileMenu = () => {
    // 等待DOM加载完成
    setTimeout(() => {
      const hamburger = document.querySelector('.VPNavBarHamburger')
      const navBarExtra = document.querySelector('.VPNavBarExtra')
      
      if (navBarExtra) {
        // 创建自定义汉堡按钮 - 更现代的设计
        const customHamburger = document.createElement('button')
        customHamburger.className = 'VPNavBarHamburger custom-mobile-menu-trigger'
        customHamburger.setAttribute('type', 'button')
        customHamburger.setAttribute('aria-label', '打开菜单')
        customHamburger.innerHTML = `
          <div class="hamburger-icon">
            <span class="line top"></span>
            <span class="line middle"></span>
            <span class="line bottom"></span>
          </div>
        `
        
        // 将自定义按钮插入到导航栏额外内容区域前面
        navBarExtra.parentNode?.insertBefore(customHamburger, navBarExtra)
        
        if (hamburger) {
          hamburger.style.display = 'none'
        }
        
        // 添加点击事件
        customHamburger.addEventListener('click', () => {
          openMobileMenu()
        })
        
        console.log('[MobileMenu] 成功替换原生移动端菜单')
      }
    }, 1000)
  }

  const tryAddIcons = () => {
    // 检查文档是否完全加载
    if (document.readyState !== 'complete') {
      window.addEventListener('load', tryAddIcons, { once: true })
      return
    }

    // 调试：检查导航栏是否存在
    const navBar = document.querySelector('.VPNavBar, nav, header')
    if (!navBar && retryCount < 10) {
      retryCount++
      setTimeout(tryAddIcons, 100)
      return
    }

    // 替换原生移动端菜单
    replaceNativeMobileMenu()

    const added = addNavIcons()
    
    // 如果添加成功或者重试次数过多，设置观察器
    if (added || retryCount >= maxRetries) {
      // 使用MutationObserver监听DOM变化（路由切换、下拉菜单展开等）
      const observer = new MutationObserver((mutations) => {
        // 检查是否有下拉菜单展开
        let shouldUpdate = false
        mutations.forEach((mutation) => {
          if (mutation.type === 'attributes' && mutation.attributeName === 'aria-expanded') {
            shouldUpdate = true
          }
          if (mutation.addedNodes.length > 0) {
            mutation.addedNodes.forEach((node) => {
              if (node.nodeType === Node.ELEMENT_NODE) {
                const el = node as Element
                if (el.classList.contains('VPNavBarMenuGroupContent') || 
                    el.querySelector('.VPNavBarMenuGroupContent')) {
                  shouldUpdate = true
                }
              }
            })
          }
        })
        
        if (shouldUpdate) {
          // 防抖，避免频繁调用
          clearTimeout((window as any).__navIconTimer)
          ;(window as any).__navIconTimer = setTimeout(() => {
            addNavIcons()
          }, 150)
        }
      })
      
      if (document.body) {
        observer.observe(document.body, { 
          childList: true, 
          subtree: true,
          attributes: true,
          attributeFilter: ['aria-expanded', 'class']
        })
      }
      
      // 监听下拉菜单的hover事件，确保展开时添加图标
      const menuGroups = document.querySelectorAll('.VPNavBarMenuGroup')
      menuGroups.forEach((group) => {
        group.addEventListener('mouseenter', () => {
          setTimeout(() => {
            addNavIcons()
          }, 100)
        })
      })
      
      // 也监听路由变化
      const handleRouteChange = () => {
        setTimeout(() => {
          addNavIcons()
          // 路由变化时重新修复移动端菜单
          setTimeout(fixMobileMenu, 100)
        }, 200)
      }
      
      window.addEventListener('popstate', handleRouteChange)
      window.addEventListener('pushstate', handleRouteChange, true)

      // 监听 VitePress 路由变化
      const originalPushState = history.pushState
      history.pushState = function(...args) {
        originalPushState.apply(history, args)
        handleRouteChange()
      }
    } else {
      retryCount++
      setTimeout(tryAddIcons, 100)
    }
  }

  // 等待DOM和Iconify都加载完成
  if (document.readyState === 'complete') {
    setTimeout(tryAddIcons, 500)
  } else {
    window.addEventListener('load', () => {
      setTimeout(tryAddIcons, 500)
    }, { once: true })
  }
})
</script>

<template>
  <NoticeManager />
  <DefaultTheme.Layout />
  <ChineseConverter style="display:none" />
  
  <!-- 自定义移动端菜单 -->
  <MobileMenu :is-open="isMobileMenuOpen" @close="closeMobileMenu" />
</template>

<style>
::view-transition-old(root),
::view-transition-new(root) {
  animation: none;
  mix-blend-mode: normal;
}

::view-transition-old(root),
.dark::view-transition-new(root) {
  z-index: 1;
}

::view-transition-new(root),
.dark::view-transition-old(root) {
  z-index: 0;
}

.chinese-converting body {
  opacity: 0.92;
  transition: opacity 0.12s ease;
}

.VPSwitchAppearance {
  width: 22px !important;
}

.VPSwitchAppearance .check {
  transform: none !important;
}

/* 自定义移动端汉堡菜单按钮样式 - 现代设计 */
.custom-mobile-menu-trigger {
  position: relative !important;
  width: 44px !important;
  height: 44px !important;
  color: var(--vp-c-text-1) !important;
  transition: all 0.3s ease !important;
  background: none !important;
  border: none !important;
  padding: 8px !important;
  cursor: pointer !important;
  display: flex !important;
  margin: 0 !important;
  border-radius: 8px !important;
  outline: none !important;
}

.custom-mobile-menu-trigger:hover {
  color: var(--vp-c-brand) !important;
  background: var(--vp-c-bg-soft) !important;
  transform: scale(1.05) !important;
}

.custom-mobile-menu-trigger:active {
  transform: scale(0.95) !important;
}

.custom-mobile-menu-trigger:focus-visible {
  box-shadow: 0 0 0 2px var(--vp-c-brand) !important;
}

.hamburger-icon {
  position: relative !important;
  width: 24px !important;
  height: 24px !important;
  display: flex !important;
  flex-direction: column !important;
  justify-content: center !important;
  align-items: center !important;
  gap: 4px !important;
}

.hamburger-icon .line {
  width: 20px !important;
  height: 2px !important;
  background-color: currentColor !important;
  border-radius: 2px !important;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1) !important;
  transform-origin: center !important;
}

.hamburger-icon .line.top {
  transform: translateY(-2px) !important;
}

.hamburger-icon .line.bottom {
  transform: translateY(2px) !important;
}

/* 菜单打开时的动画效果 */
.custom-mobile-menu-trigger.active .hamburger-icon .line.top {
  transform: translateY(2px) rotate(45deg) !important;
}

.custom-mobile-menu-trigger.active .hamburger-icon .line.middle {
  opacity: 0 !important;
  transform: scale(0) !important;
}

.custom-mobile-menu-trigger.active .hamburger-icon .line.bottom {
  transform: translateY(-2px) rotate(-45deg) !important;
}

/* 在移动端显示自定义菜单按钮 */
@media (max-width: 768px) {
  .custom-mobile-menu-trigger {
    display: flex !important;
    align-items: center !important;
    justify-content: center !important;
  }
  
  /* 隐藏原有的移动端汉堡菜单 */
  :deep(.VPNavBarHamburger) {
    display: none !important;
  }
  
  /* 调整导航栏布局 */
  :deep(.VPNavBar) {
    position: relative;
  }
  
  :deep(.VPNavBarTitle) {
    flex: 1;
  }
  
  :deep(.VPNavBarNav) {
    display: none !important;
  }
  
  /* 隐藏原有的额外按钮区域 */
  :deep(.VPNavBarExtra) {
    display: none !important;
  }
  /* 隐藏桌面端默认导航与语言切换等 */
  :deep(.VPNavBarMenu),
  :deep(.VPNavBarMenuLink),
  :deep(.VPNavBarMenuGroup),
  :deep(.VPNavBarMenuGroupContent),
  :deep(.VPNavBarNav),
  :deep(.VPNavBarExtra),
  :deep(.VPNavBarSearch),
  :deep(.VPSocialLinks),
  :deep(.VPNavBarAppearance),
  :deep(.VPNavBarLocaleMenu),
  :deep(.VPNavBarLocales),
  :deep(.VPLocaleSwitch),
  :deep(.VPLocaleMenu),
  :deep(.VPNavBarLocaleSwitch),
  :deep(.VPNavBarLocaleSelector),
  :deep([data-testid="locale-switch"]),
  :deep(.VPNavBarLangSwitch) {
    display: none !important;
  }

  .mobile-menu-open :deep(.VPNavBarTitle) {
    display: none !important;
  }
}
</style>
