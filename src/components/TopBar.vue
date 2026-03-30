<script setup>
import { ref } from 'vue'
import LoginModal from '@/components/LoginModal.vue'
import { useUserStore } from '@/stores/userStore'
import { storeToRefs } from 'pinia'
const store = useUserStore()
const {userInfo}=storeToRefs(store)
const props = defineProps({ currentView: { type: String, default: 'dashboard' } })
const emit = defineEmits(['update:currentView', 'user-logged-in'])

const showLogin = ref(false)
const user = ref({ name: '访客', avatar: '' })
const fileInput = ref(null)

function openLogin() { showLogin.value = true }
function onLogin(payload) {
  // 这里只做演示：将用户名显示到头像旁
  user.value.name = payload.username || '用户'
  user.value.avatar = ''
  emit('user-logged-in', payload)
}

function changeView(v) { emit('update:currentView', v) }

const changeavatar = () => {
  if (fileInput.value) fileInput.value.click()
}

function onFileChange(e) {
  const file = e.target && e.target.files && e.target.files[0]
  if (!file) return
  // 使用 object URL 快速显示（生产可替换为上传后返回的地址）
  const url = URL.createObjectURL(file)
  // 更新 pinia store 中的 avatar
  userInfo.value.avatar = url
  // 更新本地副本
  user.value.avatar = url
  // 清空 input，便于下次选择同一文件
  e.target.value = ''
}
</script>

<template>
  <header class="topbar">
    <div class="left">
      <div class="brand">🎓 职业规划平台111</div>
      <nav class="nav">
        <button :class="{ active: currentView === 'dashboard' }" @click="changeView('dashboard')">个人成长画像</button>
        <button :class="{ active: currentView === 'resources' }" @click="changeView('resources')">资源整合库</button>
      </nav>
    </div>

    <div class="right">
      <button class="login-btn" @click="openLogin">登录 / 注册</button>

      <div class="avatar-wrap">
          <input ref="fileInput" type="file" accept="image/*" @change="onFileChange" style="display:none" />
        <img class="avatar-img" :src="user.avatar || userInfo.avatar" alt="avatar" />
        <div class="avatar-dropdown">
          <button class="dropdown-item" @click="changeavatar">修改头像</button>
          <button class="dropdown-item">个人信息</button>
          <!-- 其他选项占位 -->
        </div>
      </div>
    </div>

    <LoginModal v-model="showLogin" @login="onLogin" />
  </header>
</template>

<style scoped>
.topbar { display:flex; justify-content:space-between; align-items:center; padding:12px 20px; background:#fff; box-shadow:0 1px 3px rgba(0,0,0,0.06); border-radius:6px }
.topbar .left { display:flex; align-items:center; gap:18px }
.brand { font-weight:700; color:var(--primary-color); font-size:1.05rem }
.nav button { background:transparent; border:none; padding:8px 12px; margin-right:6px; color:#6b7280; cursor:pointer; border-radius:6px }
.nav button.active { color:var(--primary-color); border-bottom:2px solid var(--primary-color); }
.right { display:flex; align-items:center; gap:12px }
.login-btn { background:transparent; border:1px solid var(--primary-color); color:var(--primary-color); padding:6px 10px; border-radius:8px; cursor:pointer }
.avatar-wrap { position:relative }
.avatar-img { width:40px; height:40px; border-radius:50%; border:2px solid #f3f4f6; cursor:pointer }
.avatar-wrap:hover .avatar-dropdown { display:block }
.avatar-dropdown { position:absolute; right:0; top:48px; display:none; min-width:140px; background:#fff; border-radius:8px; padding:8px 0; box-shadow:0 8px 20px rgba(2,6,23,0.08); z-index:40 }
.dropdown-item { width:100%; text-align:center; background:transparent; border:none; padding:8px 12px; cursor:pointer; color:#374151 }
.dropdown-item:hover { background:#f3f4f6 }

@media (max-width:700px) {
  .nav { display:none }
}
</style>
