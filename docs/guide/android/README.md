---
title: 安卓版 - 快速开始
permalink: /guide/android/
createTime: 2025/11/01 22:41:17
---

# TuneFreeNext Android 版

> 音遇自由，音乐无界

> [!IMPORTANT]
>
> TuneFreeNext Android 版采用 Kotlin + Jetpack Compose 开发，为你带来原生级流畅体验。本项目仅供个人学习研究使用，禁止用于商业及非法用途。

## ::mdi:cellphone-screenshot:: 预览

<div class="screenshot-carousel" style="position: relative; margin: 20px 0;">
  <button class="carousel-btn carousel-btn-prev" onclick="this.parentElement.querySelector('.carousel-container').scrollLeft -= 350" style="position: absolute; left: 10px; top: 50%; transform: translateY(-50%); z-index: 10; background: rgba(0,0,0,0.5); color: white; border: none; width: 40px; height: 40px; border-radius: 50%; cursor: pointer; display: flex; align-items: center; justify-content: center; transition: all 0.3s;">
    <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="15 18 9 12 15 6"></polyline></svg>
  </button>
  
  <div class="carousel-container" style="overflow-x: auto; scroll-behavior: smooth; scrollbar-width: none; -ms-overflow-style: none;">
    <div style="display: flex; gap: 15px; padding: 10px 0;">
      <img src="/images/android/121a7aa3bee5abe38478c42ef423f19a.png" alt="TuneFreeNext 截图 1" style="height: 500px; width: auto; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.15); flex-shrink: 0;">
      <img src="/images/android/58da4324c84ae75ef4b5f809087404a0.png" alt="TuneFreeNext 截图 2" style="height: 500px; width: auto; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.15); flex-shrink: 0;">
      <img src="/images/android/03d8b8187827c9c0cb2cc2cd693ced07.png" alt="TuneFreeNext 截图 3" style="height: 500px; width: auto; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.15); flex-shrink: 0;">
      <img src="/images/android/14add17abb8ab774ad0cc7b0a957cca2.png" alt="TuneFreeNext 截图 4" style="height: 500px; width: auto; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.15); flex-shrink: 0;">
    </div>
  </div>
  
  <button class="carousel-btn carousel-btn-next" onclick="this.parentElement.querySelector('.carousel-container').scrollLeft += 350" style="position: absolute; right: 10px; top: 50%; transform: translateY(-50%); z-index: 10; background: rgba(0,0,0,0.5); color: white; border: none; width: 40px; height: 40px; border-radius: 50%; cursor: pointer; display: flex; align-items: center; justify-content: center; transition: all 0.3s;">
    <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="9 18 15 12 9 6"></polyline></svg>
  </button>
</div>

<style scoped>
.carousel-container::-webkit-scrollbar {
  display: none;
}

.carousel-btn:hover {
  background: rgba(0,0,0,0.7) !important;
  transform: translateY(-50%) scale(1.1) !important;
}

.carousel-btn:active {
  transform: translateY(-50%) scale(0.95) !important;
}
</style>

## ::material-symbols:star:: 核心功能

::: tabs

@tab ::material-symbols:search:: 搜索

::mdi:music-box-multiple-outline:: 多平台搜索（网易云、QQ音乐、酷我）

::material-symbols:alt-route:: 聚合搜索，一次搜索所有平台

::material-symbols:history:: 搜索历史记录，音源智能记忆

@tab ::material-symbols:play-circle:: 播放

::material-symbols:graphic-eq:: 320k 高音质在线播放

::material-symbols:repeat:: 多种播放模式（顺序/循环/随机/单曲）

::material-symbols:bookmark:: 断点续播，自动恢复播放进度

::material-symbols:blur-on:: 淡入淡出效果（播放/暂停/切歌）

::material-symbols:timer:: 定时关闭播放

::material-symbols:notifications-active:: 通知栏媒体控制

::material-symbols:cloud-circle:: 前台服务，后台稳定运行

@tab ::material-symbols:lyrics:: 歌词

::material-symbols:sync:: 实时滚动歌词显示

::material-symbols:translate:: 支持歌词翻译

::material-symbols:blur-circular:: 歌词模糊效果（可开关）

@tab ::material-symbols:playlist-add:: 歌单

::material-symbols:folder:: 创建、编辑、删除、搜索歌单

::mingcute:netease-music-fill:: 导入网易云歌单（新建/合并/覆盖）

::material-symbols:sync-alt:: 定时自动更新网易云歌单

::material-symbols:favorite:: "我喜欢的音乐"系统歌单

::material-symbols:view-list:: 分页显示（每页100首）

@tab ::material-symbols:download:: 下载

::material-symbols:high-quality:: 多音质下载（128k/320k/FLAC/FLAC 24bit）

::material-symbols:bolt:: 批量下载，并发控制

::material-symbols:description:: 自动写入元数据（歌名、歌手、专辑、歌词、封面）

::material-symbols:folder-managed:: 下载管理（全部/下载中/已完成）

@tab ::material-symbols:cloud-sync:: 云端

::material-symbols:person:: 邮箱注册登录，验证码验证

::material-symbols:backup:: 云端备份歌单和播放列表

::material-symbols:devices:: 跨设备数据同步

@tab ::material-symbols:settings:: 设置

::material-symbols:palette:: 主题切换（浅色/深色/跟随系统）

::material-symbols:tune:: 自定义播放音质

::material-symbols:storage:: 缓存管理（查看、清理、配置大小）

::material-symbols:update:: 自动检测版本更新

::material-symbols:build:: 数据修复功能

:::

## ::material-symbols:download:: 下载应用

<div id="android-version-info" style="margin: 20px 0;">
  <div class="version-loading" style="text-align: center; padding: 20px; color: var(--vp-c-text-2);">
    正在加载版本信息...
  </div>
</div>

<script setup>
import { onMounted } from 'vue'

onMounted(() => {
  fetch('https://ums.sayqz.com/api/user/1000/android/1.0.0/ini')
    .then(response => response.json())
    .then(data => {
      if (data.code === 0) {
        const versionData = data.data.version
        const noticeData = data.data.notice
        
        const container = document.getElementById('android-version-info')
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
      const container = document.getElementById('android-version-info')
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

