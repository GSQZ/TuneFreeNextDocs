---
title: 功能介绍
permalink: /guide/features
createTime: 2025/01/30 12:00:00
---

# 功能介绍

TuneFreeNext V3 基于 Flutter 开发，采用 Material Design 3 设计语言，为你带来丰富的音乐播放功能。

<!-- 截图展示区域 - 等待更新 -->

## ::material-symbols:star:: 核心功能

::: tabs

@tab ::material-symbols:search:: 搜索

**多源聚合搜索**

::mdi:music-box-multiple-outline:: 支持多个主流音乐平台

::material-symbols:alt-route:: 一键切换音源，同一关键词自动重新搜索

::material-symbols:history:: 搜索历史本地存储，最多保留 20 条记录

::material-symbols:whatshot:: 热门搜索推荐

**搜索类型**

支持歌曲搜索、歌手搜索、歌单搜索（部分音源可能不支持某些搜索类型）

@tab ::material-symbols:play-circle:: 播放

**高品质播放**

::material-symbols:graphic-eq:: 支持 128k / 320k / FLAC / FLAC 24bit 多种音质

::material-symbols:repeat:: 四种播放模式：顺序播放、列表循环、单曲循环、随机播放

::material-symbols:bookmark:: 播放模式自动保存，下次启动自动恢复

**无缝播放体验**

::material-symbols:fast-forward:: 无缝切歌：歌曲开始播放后自动预加载下一首

::material-symbols:blur-on:: 淡入淡出效果（播放/暂停/切歌）

::material-symbols:timer:: 定时关闭：支持定时分钟数、播放完当前歌曲、播放指定歌曲数后关闭

**平台集成**

::material-symbols:notifications-active:: Android 通知栏媒体控制（带进度条、上下曲切换）

::mdi:apple:: iOS 控制中心集成

::mdi:microsoft-windows:: Windows 系统托盘（单击显示窗口、右键菜单控制）

@tab ::material-symbols:lyrics:: 歌词

**实时滚动歌词**

::material-symbols:sync:: 歌词精确同步，自动滚动到当前播放行

::material-symbols:zoom-in:: 当前行智能放大显示，视觉焦点清晰

::material-symbols:translate:: 支持歌词翻译显示

::material-symbols:touch-app:: 点击歌词行可跳转到对应播放位置

**多格式支持**

- 标准 LRC 格式
- JSON 歌词格式
- 逐字歌词格式
- 自动合并翻译歌词

@tab ::material-symbols:playlist-add:: 歌单

**本地歌单管理**

::material-symbols:folder:: 创建、编辑、删除本地歌单

::material-symbols:favorite:: 「我喜欢的音乐」收藏功能

::material-symbols:drag-indicator:: 歌曲拖拽排序

**在线歌单导入**

::material-symbols:cloud-download:: 支持导入主流平台歌单

::material-symbols:link:: 智能链接解析，自动识别来源

::material-symbols:content-copy:: 支持粘贴链接或输入歌单 ID

**收藏整个歌单**

点击歌单详情页的收藏按钮，可将整个在线歌单保存到本地

@tab ::material-symbols:download:: 下载

**多音质下载**

::material-symbols:high-quality:: 支持 128k / 320k / FLAC / FLAC 24bit

::material-symbols:bolt:: 最多 3 个并发下载，自动队列处理

**完整元数据写入**

::material-symbols:description:: 自动写入歌曲标签（歌名、歌手、专辑）

::material-symbols:image:: 嵌入专辑封面图片

::material-symbols:lyrics:: 生成独立 .lrc 歌词文件

**下载路径**

- Android: `/storage/emulated/0/Download/Music/TuneFreeNext/`
- Windows: `%USERPROFILE%\Music\TuneFreeNext\`
- macOS/Linux: `~/Music/TuneFreeNext/`

@tab ::material-symbols:cloud-sync:: 云同步

**跨设备数据同步**

::material-symbols:person:: 邮箱注册登录，验证码验证

::material-symbols:backup:: 云端备份本地歌单、收藏歌曲、应用设置

::material-symbols:devices:: 多设备数据同步，换机不丢失

**同步内容**

- 本地歌单（包含所有歌曲）
- 收藏歌曲列表
- 应用设置（主题、音源、音质等）

@tab ::material-symbols:history:: 播放历史

**自动记录**

::material-symbols:timer:: 播放超过 5 秒自动记录

::material-symbols:merge:: 相同歌曲合并显示（播放次数、总时长）

**统计分析**

::material-symbols:insights:: 今日/本周/本月/本年 多维度统计

::material-symbols:calendar-month:: 年度播放热力图（类似 GitHub 贡献图）

::material-symbols:leaderboard:: 最常播放歌曲 Top 10 排行榜

::material-symbols:touch-app:: 点击日期查看当日播放详情

@tab ::material-symbols:settings:: 设置

**外观设置**

::material-symbols:palette:: 主题切换：浅色/深色/跟随系统

::material-symbols:colorize:: 12 种预设主题色可选

::material-symbols:auto-awesome:: 莫奈取色：从封面自动提取主题色

**播放设置**

::material-symbols:tune:: 默认播放音质选择

::material-symbols:download:: 下载音质选择

::material-symbols:fast-forward:: 无缝播放开关

**缓存管理**

::material-symbols:storage:: 歌曲缓存：自动缓存播放过的歌曲

::material-symbols:cleaning-services:: 缓存大小限制、过期时间设置

::material-symbols:delete:: 一键清理缓存

**桌面端专属**

::material-symbols:dock-to-left:: 关闭到托盘（可选直接退出）

::material-symbols:volume-up:: 音量滑块控制

:::

## ::material-symbols:devices:: 自适应布局

TuneFreeNext 针对不同屏幕尺寸进行了精心适配：

### 竖屏模式（手机）

- 底部导航栏（支持滚动隐藏）
- 横向滚动的推荐列表
- 64dp 紧凑版迷你播放栏
- 全屏播放页封面/歌词点击切换

### 横屏模式（平板/桌面）

- 侧边导航栏（600-1200dp 紧凑，>1200dp 展开）
- 网格布局的推荐列表（自适应 2-6 列）
- 72dp 增强版播放栏（带进度条拖动、音质显示、播放模式）
- 全屏播放页左右分屏（封面控制 + 歌词）
- 歌单详情页分栏布局（左侧信息 + 右侧列表）

## ::material-symbols:download:: 立即体验

准备好了吗？前往下载页面获取 TuneFreeNext：

[下载安装 →](/guide/download)
