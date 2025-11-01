---
title: Windows版 - 快速开始
permalink: /guide/windows/
createTime: 2025/11/01 22:41:50
---

# TuneFreeNext Windows 版

> 音遇自由，音乐无界

> [!IMPORTANT]
>
> TuneFreeNext Windows 版基于 Electron + Vue 3 开发，为你带来流畅的桌面体验。本项目仅供个人学习研究使用，禁止用于商业及非法用途。

## ::material-symbols:download:: 下载应用

<div id="windows-version-info" style="margin: 20px 0;">
  <div class="version-loading" style="text-align: center; padding: 20px; color: var(--vp-c-text-2);">
    正在加载版本信息...
  </div>
</div>

<script setup>
import { onMounted } from 'vue'

onMounted(() => {
  fetch('https://ums.sayqz.com/api/user/1000/pc/1.0.0/ini')
    .then(response => response.json())
    .then(data => {
      if (data.code === 0) {
        const versionData = data.data.version
        const noticeData = data.data.notice
        
        const container = document.getElementById('windows-version-info')
        if (container) {
          let html = '<div style="background: var(--vp-c-bg-soft); border-radius: 8px; padding: 20px; margin-bottom: 20px;">'
          
          html += '<div style="display: flex; align-items: center; justify-content: space-between; margin-bottom: 20px; flex-wrap: wrap; gap: 15px;">'
          html += '<h3 style="margin: 0; font-size: 18px; color: var(--vp-c-text-1);">最新版本：v' + versionData.latest + '</h3>'
          html += '<a href="' + versionData.latest_url + '" target="_blank" style="display: inline-block; padding: 10px 20px; background: var(--vp-c-brand-1); color: var(--vp-c-bg); border-radius: 6px; text-decoration: none; font-weight: 500; font-size: 14px; transition: all 0.2s; border: 1px solid var(--vp-c-brand-1);">'
          html += '下载 v' + versionData.latest + '</a></div>'
          
          if (noticeData && noticeData.content) {
            html += '<div style="background: var(--vp-c-warning-soft); border-left: 4px solid var(--vp-c-warning); padding: 12px 15px; border-radius: 4px; margin-bottom: 15px;">'
            html += '<div style="font-weight: 600; margin-bottom: 8px; display: flex; align-items: center; gap: 8px;">'
            html += '<svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="currentColor"><path d="M12 2L1 21h22L12 2zm0 3.83L19.53 19H4.47L12 5.83zM11 16h2v2h-2v-2zm0-6h2v4h-2v-4z"/></svg>'
            html += '<span>公告</span></div>'
            html += '<div style="color: var(--vp-c-text-2); white-space: pre-line; line-height: 1.6;">' + noticeData.content + '</div></div>'
          }
          
          html += '<div style="margin-top: 15px;">'
          html += '<div style="font-weight: 600; margin-bottom: 10px; display: flex; align-items: center; gap: 8px;">'
          html += '<svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"></path><polyline points="14 2 14 8 20 8"></polyline><line x1="16" y1="13" x2="8" y2="13"></line><line x1="16" y1="17" x2="8" y2="17"></line><polyline points="10 9 9 9 8 9"></polyline></svg>'
          html += '<span>更新日志</span></div>'
          html += '<div style="color: var(--vp-c-text-2); line-height: 1.8; white-space: pre-line; padding-left: 10px;">' + versionData.latest_content + '</div></div></div>'
          
          container.innerHTML = html
        }
      }
    })
    .catch(error => {
      console.error('获取版本信息失败:', error)
      const container = document.getElementById('windows-version-info')
      if (container) {
        container.innerHTML = '<div style="text-align: center; padding: 20px; color: var(--vp-c-danger); display: flex; align-items: center; justify-content: center; gap: 8px;">' +
          '<svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle><line x1="12" y1="8" x2="12" y2="12"></line><line x1="12" y1="16" x2="12.01" y2="16"></line></svg>' +
          '<span>加载版本信息失败，请稍后重试</span></div>'
      }
    })
})
</script>

## ::material-symbols:gavel:: 免责声明

本项目仅供个人学习研究使用，禁止用于商业及非法用途。使用本软件所产生的一切后果由使用者自行承担，开发者不承担任何责任。

