<script setup lang="ts">
import { ref, onMounted } from 'vue'

const isLoggedIn = ref(false)
const isLoading = ref(false)
const loginError = ref('')
const email = ref('')
const password = ref('')
const userInfo = ref<any>(null)
const token = ref('')
const extendData = ref<any>({})
const dataStatus = ref<'loading' | 'checked' | 'error'>('loading')
const dataStatusMessage = ref('')
const hasOldData = ref(false)
const isMigrating = ref(false)
const migrateResult = ref<{type: 'success' | 'error' | 'warning' | '', message: string}>({type: '', message: ''})
const confirmOverwrite = ref(false)

// 解析后的云备份数据
const parsedBackupData = ref<any>(null)
const parseError = ref('')

// Base64 解码（支持 UTF-8）
const base64Decode = (str: string): string => {
  // 使用 TextDecoder 正确处理 UTF-8
  const binaryStr = atob(str)
  const bytes = new Uint8Array(binaryStr.length)
  for (let i = 0; i < binaryStr.length; i++) {
    bytes[i] = binaryStr.charCodeAt(i)
  }
  return new TextDecoder('utf-8').decode(bytes)
}

// 解码云备份数据
const decodeCloudBackup = (encoded: string): any => {
  try {
    let data = encoded
    console.log('=== 开始解码云备份数据 ===')
    console.log('原始数据长度:', data.length)
    console.log('原始数据前100字符:', data.substring(0, 100))
    
    // 步骤1：检测是否有 % 号，有则 URL 解码
    if (data.includes('%')) {
      console.log('步骤1: 检测到 % 号，进行 URL 解码')
      data = decodeURIComponent(data)
      console.log('URL 解码后前100字符:', data.substring(0, 100))
    } else {
      console.log('步骤1: 无 % 号，跳过 URL 解码')
    }
    
    // 步骤2：Base64 解码（使用 UTF-8）
    console.log('步骤2: Base64 解码')
    data = base64Decode(data)
    console.log('Base64 解码后前200字符:', data.substring(0, 200))
    
    // 步骤3：检测是否以 { 或 [ 开头，否则 URI 解码
    if (!data.startsWith('{') && !data.startsWith('[')) {
      console.log('步骤3: 不是 JSON 格式，进行 URI 解码')
      data = decodeURIComponent(data)
      console.log('URI 解码后前200字符:', data.substring(0, 200))
    } else {
      console.log('步骤3: 已是 JSON 格式，跳过 URI 解码')
    }
    
    // 步骤4：解析 JSON
    console.log('步骤4: 解析 JSON')
    const result = JSON.parse(data)
    console.log('解析成功，歌单数量:', result.playlists?.length)
    console.log('=== 解码完成 ===')
    return result
  } catch (error) {
    console.error('解码云备份数据失败:', error)
    throw new Error('数据格式错误，无法解析')
  }
}

// 生成随机机器码
const generateUdid = () => {
  const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789'
  let udid = ''
  for (let i = 0; i < 16; i++) {
    udid += chars.charAt(Math.floor(Math.random() * chars.length))
  }
  return udid
}

const handleLogin = async () => {
  if (!email.value.trim() || !password.value) {
    loginError.value = '请输入邮箱和密码'
    return
  }
  
  isLoading.value = true
  loginError.value = ''
  
  try {
    const udid = generateUdid()
    const formData = new FormData()
    formData.append('account', email.value.trim())
    formData.append('password', password.value)
    formData.append('udid', udid)
    
    const response = await fetch('https://ums.sayqz.com/api/user/1000/V3/3.0.0/logon', {
      method: 'POST',
      body: formData
    })
    const data = await response.json()
    
    if (data.code === 0 && data.data) {
      token.value = data.data.token
      userInfo.value = data.data.info
      isLoggedIn.value = true
      checkCloudData()
    } else {
      loginError.value = data.msg || '登录失败，请检查邮箱和密码'
    }
  } catch (error) {
    loginError.value = '网络错误，请稍后重试'
  } finally {
    isLoading.value = false
  }
}

const handleLogout = () => {
  isLoggedIn.value = false
  token.value = ''
  userInfo.value = null
  email.value = ''
  password.value = ''
  extendData.value = {}
  hasOldData.value = false
  confirmOverwrite.value = false
}

const checkCloudData = async () => {
  dataStatus.value = 'loading'
  hasOldData.value = false
  
  try {
    const formData = new FormData()
    formData.append('token', token.value)
    
    const response = await fetch('https://ums.sayqz.com/api/user/1000/V3/3.0.0/info', {
      method: 'POST',
      body: formData
    })
    const data = await response.json()
    
    if (data.code === 0 && data.data) {
      const extend = data.data.extend || {}
      extendData.value = extend
      
      // 更新用户信息（可能比登录时更完整）
      userInfo.value = data.data
      
      // 检测是否有旧版云备份数据
      if (extend.CloudBackup) {
        hasOldData.value = true
        // 解析云备份数据
        try {
          parsedBackupData.value = decodeCloudBackup(extend.CloudBackup)
          parseError.value = ''
        } catch (error: any) {
          parseError.value = error.message
          parsedBackupData.value = null
        }
      }
      
      dataStatus.value = 'checked'
    } else {
      dataStatus.value = 'error'
      dataStatusMessage.value = data.msg || '获取数据失败'
    }
  } catch (error) {
    dataStatus.value = 'error'
    dataStatusMessage.value = '网络错误，请刷新页面重试'
  }
}

// 将旧版歌曲转换为新版 SyncSong 格式
const convertToSyncSong = (oldSong: any) => {
  const syncSong: any = {
    sid: String(oldSong.id),
    n: oldSong.name || oldSong.title,
    ar: oldSong.artist || oldSong.singer,
    s: oldSong.source || oldSong.platform || 'netease'
  }
  
  if (oldSong.album) syncSong.al = oldSong.album
  // 封面不迁移，V3 会自动获取
  if (oldSong.duration && oldSong.duration > 0) {
    syncSong.dm = oldSong.duration * 1000 // 秒转毫秒
  }
  
  return syncSong
}

// 将旧版歌单转换为新版 SyncPlaylist 格式
const convertToSyncPlaylist = (oldPlaylist: any) => {
  return {
    n: oldPlaylist.name,
    d: oldPlaylist.description || '',
    songs: (oldPlaylist.songs || []).map(convertToSyncSong)
  }
}

// URL-safe Base64 编码
const base64UrlEncode = (obj: any): string => {
  const jsonStr = JSON.stringify(obj)
  const utf8Bytes = new TextEncoder().encode(jsonStr)
  let binaryStr = ''
  for (let i = 0; i < utf8Bytes.length; i++) {
    binaryStr += String.fromCharCode(utf8Bytes[i])
  }
  // 转为 URL-safe Base64: + -> -, / -> _, 去掉 =
  return btoa(binaryStr)
    .replace(/\+/g, '-')
    .replace(/\//g, '_')
    .replace(/=+$/, '')
}

// 上传到 setExtend
const uploadExtend = async (key: string, value: string): Promise<boolean> => {
  const formData = new FormData()
  formData.append('token', token.value)
  formData.append('key', key)
  formData.append('value', value)
  
  const response = await fetch('https://ums.sayqz.com/api/user/1000/V3/3.0.0/setExtend', {
    method: 'POST',
    body: formData
  })
  const data = await response.json()
  return data.code === 0
}

const handleMigrate = async () => {
  if (!parsedBackupData.value) {
    migrateResult.value = { type: 'error', message: '没有可迁移的数据' }
    return
  }
  
  isMigrating.value = true
  migrateResult.value = { type: '', message: '' }
  
  try {
    const oldData = parsedBackupData.value
    const now = Date.now()
    let uploadedCount = 0
    
    // 1. 处理歌单数据
    // 分离系统歌单(我喜欢的音乐 id=999999)和普通歌单
    const isSystemPlaylist = (p: any) => String(p.id) === '999999' || p.isSystem === true
    const systemPlaylist = oldData.playlists?.find(isSystemPlaylist)
    const normalPlaylists = oldData.playlists?.filter((p: any) => !isSystemPlaylist(p)) || []
    
    console.log('系统歌单:', systemPlaylist)
    console.log('普通歌单数量:', normalPlaylists.length)
    
    // 2. 上传收藏数据 (fav) - 来自系统歌单"我喜欢的音乐"
    if (systemPlaylist && systemPlaylist.songs?.length > 0) {
      const favData = {
        v: 1,
        t: now,
        songs: systemPlaylist.songs.map(convertToSyncSong)
      }
      console.log('上传收藏数据:', favData)
      const favEncoded = base64UrlEncode(favData)
      const favSuccess = await uploadExtend('fav', favEncoded)
      if (favSuccess) {
        uploadedCount++
        console.log('收藏数据上传成功')
      } else {
        throw new Error('收藏数据上传失败')
      }
    }
    
    // 3. 上传歌单数据 (pl) - 普通歌单
    if (normalPlaylists.length > 0) {
      const plData = {
        v: 1,
        t: now,
        pl: normalPlaylists.map(convertToSyncPlaylist)
      }
      console.log('上传歌单数据:', plData)
      const plEncoded = base64UrlEncode(plData)
      const plSuccess = await uploadExtend('pl', plEncoded)
      if (plSuccess) {
        uploadedCount++
        console.log('歌单数据上传成功')
      } else {
        throw new Error('歌单数据上传失败')
      }
    }
    
    // 统计迁移结果
    const favCount = systemPlaylist?.songs?.length || 0
    const plCount = normalPlaylists.length
    const songCount = normalPlaylists.reduce((sum: number, p: any) => sum + (p.songs?.length || 0), 0)
    
    migrateResult.value = { 
      type: 'success', 
      message: `已成功迁移 ${favCount} 首收藏、${plCount} 个歌单（共 ${songCount} 首歌曲）。请在 V3 客户端中重新登录账号，然后下载云端数据即可恢复。` 
    }
    
    // 重新检测数据
    checkCloudData()
  } catch (error: any) {
    console.error('迁移失败:', error)
    migrateResult.value = { type: 'error', message: '迁移失败：' + error.message }
  } finally {
    isMigrating.value = false
  }
}

// 格式化日期
const formatDate = (isoString: string) => {
  try {
    const date = new Date(isoString)
    return date.toLocaleString('zh-CN', {
      year: 'numeric',
      month: '2-digit',
      day: '2-digit',
      hour: '2-digit',
      minute: '2-digit'
    })
  } catch {
    return isoString
  }
}

// 计算总歌曲数
const getTotalSongsCount = () => {
  if (!parsedBackupData.value) return 0
  let total = 0
  if (parsedBackupData.value.playlists) {
    for (const playlist of parsedBackupData.value.playlists) {
      total += playlist.songs?.length || 0
    }
  }
  return total
}

const getVipStatus = () => {
  if (!userInfo.value) return '普通用户'
  const vipExpDate = userInfo.value.vipExpDate
  const vipExpTime = userInfo.value.vipExpTime
  // vipExpTime > 0 表示是 VIP
  if (vipExpTime && vipExpTime > 0) {
    return `VIP会员 · 到期时间：${vipExpDate}`
  }
  return vipExpDate || '普通用户'
}
</script>

<template>
  <div class="migrate-app">
    <!-- 登录表单 -->
    <div v-if="!isLoggedIn" class="login-section">
      <h3 class="section-title">
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"></path><circle cx="12" cy="7" r="4"></circle></svg>
        账号登录
      </h3>
      
      <div class="form-group">
        <label>邮箱</label>
        <input type="email" v-model="email" placeholder="请输入邮箱" @keyup.enter="handleLogin">
      </div>
      
      <div class="form-group">
        <label>密码</label>
        <input type="password" v-model="password" placeholder="请输入密码" @keyup.enter="handleLogin">
      </div>
      
      <button class="primary-btn" @click="handleLogin" :disabled="isLoading">
        {{ isLoading ? '登录中...' : '登录' }}
      </button>
      
      <div v-if="loginError" class="error-msg">{{ loginError }}</div>
    </div>
    
    <!-- 迁移操作区 -->
    <div v-else class="migrate-section">
      <!-- 用户信息 -->
      <div class="user-card">
        <div class="user-info">
          <div class="avatar">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"></path><circle cx="12" cy="7" r="4"></circle></svg>
          </div>
          <div class="user-details">
            <div class="user-email">{{ userInfo?.email || userInfo?.acctno || userInfo?.name || '用户' }}</div>
            <div class="user-vip">{{ getVipStatus() }}</div>
          </div>
        </div>
        <button class="outline-btn" @click="handleLogout">退出登录</button>
      </div>
      
      <!-- 数据检测 -->
      <div class="data-section">
        <h3 class="section-title">
          <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="11" cy="11" r="8"></circle><path d="m21 21-4.3-4.3"></path></svg>
          数据检测
        </h3>
        
        <div class="data-status">
          <!-- 加载中 -->
          <div v-if="dataStatus === 'loading'" class="status-loading">
            <div class="spinner"></div>
            正在检测云端数据...
          </div>
          
          <!-- 检测完成有数据 -->
          <div v-else-if="dataStatus === 'checked' && hasOldData" class="status-found">
            <div class="status-item success">
              <span class="check-icon">✓</span>
              检测到 旧版云备份数据
            </div>
            
            <!-- 解析错误 -->
            <div v-if="parseError" class="parse-error">
              <span>⚠️ 数据解析失败：{{ parseError }}</span>
            </div>
            
            <!-- 数据预览 -->
            <div v-else-if="parsedBackupData" class="data-preview">
              <div class="preview-header">
                <strong>数据预览</strong>
                <span class="export-time" v-if="parsedBackupData.exportedAt">
                  备份时间：{{ formatDate(parsedBackupData.exportedAt) }}
                </span>
              </div>
              
              <!-- 歌单列表 -->
              <div v-if="parsedBackupData.playlists?.length" class="playlist-list">
                <div v-for="(playlist, index) in parsedBackupData.playlists" :key="index" class="playlist-item">
                  <div class="playlist-info">
                    <span class="playlist-icon" :class="{ system: playlist.isSystem }">
                      <svg v-if="playlist.isSystem" xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="currentColor"><path d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"/></svg>
                      <svg v-else xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M9 18V5l12-2v13"></path><circle cx="6" cy="18" r="3"></circle><circle cx="18" cy="16" r="3"></circle></svg>
                    </span>
                    <span class="playlist-name">{{ playlist.name }}</span>
                    <span class="playlist-count">{{ playlist.songs?.length || 0 }} 首歌曲</span>
                  </div>
                </div>
              </div>
              
              <!-- 当前播放队列 -->
              <div v-if="parsedBackupData.currentPlaylist?.playlist?.length" class="current-playlist">
                <div class="playlist-item">
                  <div class="playlist-info">
                    <span class="playlist-icon queue">
                      <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polygon points="5 3 19 12 5 21 5 3"></polygon></svg>
                    </span>
                    <span class="playlist-name">当前播放队列</span>
                    <span class="playlist-count">{{ parsedBackupData.currentPlaylist.playlist.length }} 首歌曲</span>
                  </div>
                </div>
              </div>
              
              <!-- 统计 -->
              <div class="data-stats">
                <span>共 {{ parsedBackupData.playlists?.length || 0 }} 个歌单</span>
                <span>·</span>
                <span>{{ getTotalSongsCount() }} 首歌曲</span>
              </div>
            </div>
          </div>
          
          <!-- 检测完成无数据 -->
          <div v-else-if="dataStatus === 'checked' && !hasOldData" class="status-empty">
            <svg xmlns="http://www.w3.org/2000/svg" width="48" height="48" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"></path><polyline points="22 4 12 14.01 9 11.01"></polyline></svg>
            <p>未检测到需要迁移的旧版数据</p>
            <p class="sub">你可能已经完成迁移，或从未在 V1/V2 使用过云同步</p>
          </div>
          
          <!-- 错误 -->
          <div v-else-if="dataStatus === 'error'" class="status-error">
            {{ dataStatusMessage }}
          </div>
        </div>
      </div>
      
      <!-- 迁移操作 -->
      <div v-if="hasOldData" class="migrate-actions">
        <h3 class="section-title">
          <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M12 3v12"></path><path d="m8 11 4 4 4-4"></path><path d="M8 5H4a2 2 0 0 0-2 2v10a2 2 0 0 0 2 2h16a2 2 0 0 0 2-2V7a2 2 0 0 0-2-2h-4"></path></svg>
          迁移操作
        </h3>
        
        <!-- 覆盖警告 -->
        <div class="warning-box">
          <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="currentColor"><path d="M12 2L1 21h22L12 2zm0 3.83L19.53 19H4.47L12 5.83zM11 16h2v2h-2v-2zm0-6h2v4h-2v-4z"/></svg>
          <div>
            <strong>注意：迁移将覆盖新版数据！</strong>
            <p>迁移操作会将旧版数据完全覆盖到新版，无法叠加合并。如果你在 V3 中已有数据，迁移后将会丢失。</p>
          </div>
        </div>
        
        <!-- 确认勾选 -->
        <label class="confirm-check">
          <input type="checkbox" v-model="confirmOverwrite">
          <span>我已了解迁移会覆盖新版数据，确认继续</span>
        </label>
        
        <button class="primary-btn danger" @click="handleMigrate" :disabled="isMigrating || !confirmOverwrite">
          {{ isMigrating ? '迁移中...' : '开始迁移' }}
        </button>
        
        <div v-if="migrateResult.type" :class="['result-msg', migrateResult.type]">
          <template v-if="migrateResult.type === 'success'">
            <strong>✓ 迁移完成</strong><br>
          </template>
          {{ migrateResult.message }}
        </div>
        
        <!-- 迁移成功后的提示 -->
        <div v-if="migrateResult.type === 'success'" class="info-box">
          <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"></circle><path d="M12 16v-4"></path><path d="M12 8h.01"></path></svg>
          <div>
            <strong>下一步操作</strong>
            <p>请在 V3 客户端中<strong>重新登录账号</strong>，然后进入「设置  云同步  下载」即可恢复数据。</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.migrate-app {
  margin: 20px 0;
}

.login-section,
.user-card,
.data-section,
.migrate-actions {
  background: var(--vp-c-bg-soft);
  border-radius: 12px;
  padding: 24px;
  margin-bottom: 20px;
}

.login-section {
  max-width: 400px;
}

.section-title {
  margin: 0 0 20px 0;
  font-size: 18px;
  display: flex;
  align-items: center;
  gap: 8px;
}

.form-group {
  margin-bottom: 16px;
}

.form-group label {
  display: block;
  margin-bottom: 6px;
  font-size: 14px;
  color: var(--vp-c-text-2);
}

.form-group input {
  width: 100%;
  padding: 10px 12px;
  border: 1px solid var(--vp-c-divider);
  border-radius: 8px;
  background: var(--vp-c-bg);
  color: var(--vp-c-text-1);
  font-size: 14px;
  box-sizing: border-box;
  transition: border-color 0.2s;
}

.form-group input:focus {
  outline: none;
  border-color: var(--vp-c-brand-1);
}

.primary-btn {
  width: 100%;
  padding: 12px;
  background: var(--vp-c-brand-1);
  color: var(--vp-c-bg);
  border: none;
  border-radius: 8px;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: opacity 0.2s;
}

.primary-btn:hover:not(:disabled) {
  opacity: 0.9;
}

.primary-btn:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.outline-btn {
  padding: 8px 16px;
  background: transparent;
  border: 1px solid var(--vp-c-divider);
  border-radius: 6px;
  color: var(--vp-c-text-2);
  font-size: 13px;
  cursor: pointer;
  transition: all 0.2s;
}

.outline-btn:hover {
  border-color: var(--vp-c-text-2);
}

.error-msg {
  margin-top: 12px;
  padding: 10px;
  background: var(--vp-c-danger-soft);
  border-radius: 6px;
  color: var(--vp-c-danger-1);
  font-size: 13px;
}

.user-card {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 12px;
}

.user-info {
  display: flex;
  align-items: center;
  gap: 12px;
}

.avatar {
  width: 48px;
  height: 48px;
  background: var(--vp-c-brand-soft);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--vp-c-brand-1);
}

.user-email {
  font-weight: 500;
  color: var(--vp-c-text-1);
}

.user-vip {
  font-size: 13px;
  color: var(--vp-c-text-2);
}

.data-status {
  padding: 16px;
  background: var(--vp-c-bg);
  border-radius: 8px;
}

.status-loading {
  text-align: center;
  color: var(--vp-c-text-2);
}

.spinner {
  width: 24px;
  height: 24px;
  border: 2px solid var(--vp-c-divider);
  border-top-color: var(--vp-c-brand-1);
  border-radius: 50%;
  animation: spin 1s linear infinite;
  margin: 0 auto 8px;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.status-found {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.status-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 12px;
  background: var(--vp-c-green-soft);
  border-radius: 6px;
  color: var(--vp-c-green-1);
}

.status-item.success {
  background: var(--vp-c-green-soft);
  color: var(--vp-c-green-1);
}

.check-icon {
  color: var(--vp-c-green-1);
}

.parse-error {
  padding: 12px;
  background: var(--vp-c-warning-soft);
  border-radius: 6px;
  color: var(--vp-c-warning-1);
  font-size: 14px;
}

.data-preview {
  background: var(--vp-c-bg-soft);
  border: 1px solid var(--vp-c-divider);
  border-radius: 8px;
  overflow: hidden;
}

.preview-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 16px;
  background: var(--vp-c-bg);
  border-bottom: 1px solid var(--vp-c-divider);
}

.preview-header strong {
  font-size: 14px;
}

.export-time {
  font-size: 12px;
  color: var(--vp-c-text-3);
}

.playlist-list,
.current-playlist {
  padding: 8px;
}

.playlist-item {
  padding: 10px 12px;
  border-radius: 6px;
  transition: background 0.2s;
}

.playlist-item:hover {
  background: var(--vp-c-bg);
}

.playlist-info {
  display: flex;
  align-items: center;
  gap: 10px;
}

.playlist-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  background: var(--vp-c-bg);
  border-radius: 6px;
  color: var(--vp-c-text-2);
}

.playlist-icon.system {
  color: var(--vp-c-danger-1);
  background: var(--vp-c-danger-soft);
}

.playlist-icon.queue {
  color: var(--vp-c-brand-1);
  background: var(--vp-c-brand-soft);
}

.playlist-name {
  flex: 1;
  font-weight: 500;
  color: var(--vp-c-text-1);
}

.playlist-count {
  font-size: 13px;
  color: var(--vp-c-text-3);
}

.data-stats {
  display: flex;
  gap: 8px;
  padding: 12px 16px;
  background: var(--vp-c-bg);
  border-top: 1px solid var(--vp-c-divider);
  font-size: 13px;
  color: var(--vp-c-text-2);
}

.status-empty {
  text-align: center;
  padding: 20px;
  color: var(--vp-c-text-2);
}

.status-empty svg {
  margin-bottom: 12px;
  opacity: 0.5;
}

.status-empty p {
  margin: 0;
}

.status-empty .sub {
  margin-top: 8px;
  font-size: 13px;
}

.status-error {
  text-align: center;
  color: var(--vp-c-danger-1);
}

.result-msg {
  margin-top: 12px;
  padding: 12px;
  border-radius: 6px;
  font-size: 14px;
}

.result-msg.success {
  background: var(--vp-c-green-soft);
  color: var(--vp-c-green-1);
}

.result-msg.error {
  background: var(--vp-c-danger-soft);
  color: var(--vp-c-danger-1);
}

.result-msg.warning {
  background: var(--vp-c-warning-soft);
  color: var(--vp-c-warning-1);
}

.warning-box {
  display: flex;
  gap: 12px;
  padding: 16px;
  background: var(--vp-c-danger-soft);
  border: 1px solid var(--vp-c-danger-2);
  border-radius: 8px;
  margin-bottom: 20px;
  color: var(--vp-c-danger-1);
}

.warning-box svg {
  flex-shrink: 0;
  margin-top: 2px;
}

.info-box {
  display: flex;
  gap: 12px;
  padding: 16px;
  background: var(--vp-c-brand-soft);
  border: 1px solid var(--vp-c-brand-2);
  border-radius: 8px;
  margin-top: 16px;
  color: var(--vp-c-brand-1);
}

.info-box svg {
  flex-shrink: 0;
  margin-top: 2px;
}

.info-box strong {
  display: block;
  margin-bottom: 4px;
}

.info-box p {
  margin: 0;
  font-size: 13px;
  opacity: 0.9;
}

.warning-box strong {
  display: block;
  margin-bottom: 4px;
}

.warning-box p {
  margin: 0;
  font-size: 13px;
  opacity: 0.9;
}

.confirm-check {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 12px;
  background: var(--vp-c-bg);
  border: 1px solid var(--vp-c-divider);
  border-radius: 8px;
  margin-bottom: 16px;
  cursor: pointer;
  font-size: 14px;
}

.confirm-check input {
  width: 18px;
  height: 18px;
  accent-color: var(--vp-c-danger-1);
}

.primary-btn.danger {
  background: var(--vp-c-danger-1);
}

.primary-btn.danger:hover:not(:disabled) {
  background: var(--vp-c-danger-2);
}
</style>
