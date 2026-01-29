---
title: 下载安装
permalink: /guide/download
createTime: 2025/01/30 12:00:00
---

# 下载安装

TuneFreeNext 基于 Flutter 开发，支持多平台运行。选择你的设备平台下载对应版本。

> [!IMPORTANT]
>
> 本项目仅供个人学习研究使用，禁止用于商业及非法用途。使用本软件所产生的一切后果由使用者自行承担，开发者不承担任何责任。

## ::material-symbols:check-circle:: 已发布平台

<div id="version-info" style="margin: 20px 0;">
  <div class="version-loading" style="text-align: center; padding: 20px; color: var(--vp-c-text-2);">
    正在加载版本信息...
  </div>
</div>

<script setup>
import { onMounted } from 'vue'

onMounted(() => {
  fetch('https://ums.sayqz.com/api/user/1000/V3/3.0.0/ini')
    .then(response => response.json())
    .then(data => {
      if (data.code === 0) {
        const versionData = data.data.version
        const noticeData = data.data.notice
        
        const container = document.getElementById('version-info')
        if (container) {
          let html = '<div style="background: var(--vp-c-bg-soft); border-radius: 8px; padding: 20px; margin-bottom: 20px;">'
          
          // 版本标题和下载按钮
          html += '<div style="display: flex; align-items: center; justify-content: space-between; margin-bottom: 20px; flex-wrap: wrap; gap: 15px;">'
          html += '<h3 style="margin: 0; font-size: 18px; color: var(--vp-c-text-1);">最新版本：v' + versionData.latest + '</h3>'
          html += '<a href="' + versionData.latest_url + '" target="_blank" style="display: inline-block; padding: 10px 20px; background: var(--vp-c-brand-1); color: var(--vp-c-bg); border-radius: 6px; text-decoration: none; font-weight: 500; font-size: 14px; transition: all 0.2s; border: 1px solid var(--vp-c-brand-1);">'
          html += '下载 v' + versionData.latest + '</a></div>'
          
          // 公告
          if (noticeData && noticeData.content) {
            html += '<div style="background: var(--vp-c-warning-soft); border-left: 4px solid var(--vp-c-warning); padding: 12px 15px; border-radius: 4px; margin-bottom: 15px;">'
            html += '<div style="font-weight: 600; margin-bottom: 8px; display: flex; align-items: center; gap: 8px;">'
            html += '<svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="currentColor"><path d="M12 2L1 21h22L12 2zm0 3.83L19.53 19H4.47L12 5.83zM11 16h2v2h-2v-2zm0-6h2v4h-2v-4z"/></svg>'
            html += '<span>公告</span></div>'
            html += '<div style="color: var(--vp-c-text-2); white-space: pre-line; line-height: 1.6;">' + noticeData.content + '</div></div>'
          }
          
          // 更新日志
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
      const container = document.getElementById('version-info')
      if (container) {
        container.innerHTML = '<div style="text-align: center; padding: 20px; color: var(--vp-c-danger); display: flex; align-items: center; justify-content: center; gap: 8px;">' +
          '<svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle><line x1="12" y1="8" x2="12" y2="12"></line><line x1="12" y1="16" x2="12.01" y2="16"></line></svg>' +
          '<span>加载版本信息失败，请稍后重试</span></div>'
      }
    })
})
</script>

### 支持的平台

:::: card-grid
::: card title="Android" icon="mdi:android"

适用于手机、平板

:::

::: card title="Windows" icon="mdi:microsoft-windows"

适用于 Windows 桌面系统

:::

::: card title="车机" icon="mdi:car"

适用于车载系统

:::

::: card title="TV" icon="mdi:television"

适用于电视端

:::
::::

## ::material-symbols:schedule:: 即将推出

以下平台版本正在开发中，敬请期待：

:::: card-grid
::: card title="iOS" icon="mdi:apple-ios"

适用于 iPhone、iPad

:::

::: card title="macOS" icon="mdi:apple"

适用于 Mac 电脑

:::

::: card title="Linux" icon="mdi:linux"

适用于 Linux 桌面系统

:::
::::

## ::material-symbols:help:: 安装遇到问题？

如果在安装过程中遇到问题，请查看 [常见问题](/guide/faq) 或 [加入 QQ 群](https://qm.qq.com/q/UmYH8A5CY8) 寻求帮助。
