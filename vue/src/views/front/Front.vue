<template>
  <div class="front-container">
    <!-- 头部导航 -->
    <header class="main-header">
      <div class="header-container">
        <!-- Logo区域 -->
        <div class="logo-container">
          <img src="../../assets/宠物天地.png" alt="宠物领养系统" class="logo-image">
          <span class="logo-text">宠物救助系统</span>
        </div>
        
        <!-- 导航菜单 - 居中显示在一行 -->
        <div class="nav-menu">
          <router-link to="/front/home" class="nav-item" :class="{ active: activeIndex === '/front/home' }">首页</router-link>
          <router-link to="/front/adopt" class="nav-item" :class="{ active: activeIndex === '/front/adopt' }">宠物领养</router-link>
          <router-link to="/front/salvation" class="nav-item" :class="{ active: activeIndex === '/front/salvation' }">流浪宠物救助</router-link>
          <router-link to="/front/feed" class="nav-item" :class="{ active: activeIndex === '/front/feed' }">宠物喂养点</router-link>
          <router-link to="/front/lost" class="nav-item" :class="{ active: activeIndex === '/front/lost' }">丢失宠物查看</router-link>
          <router-link to="/front/rescue" class="nav-item" :class="{ active: activeIndex === '/front/rescue' }">流浪宠物救助站</router-link>
          <router-link to="/front/article" class="nav-item" :class="{ active: activeIndex === '/front/article' }">宠物论坛</router-link>
          <router-link to="/front/donate" class="nav-item" :class="{ active: activeIndex === '/front/donate' }">宠物捐赠</router-link>
          <router-link to="/front/articleKp" class="nav-item" :class="{ active: activeIndex === '/front/articleKp' }">科普文章</router-link>
          <router-link to="/front/activity" class="nav-item" :class="{ active: activeIndex === '/front/activity' }">热门活动</router-link>
        </div>
        
        <!-- 用户区域 -->
        <div class="user-container">
          <div v-if="!user.username" class="auth-buttons">
            <el-button type="primary" size="small" @click="$router.push('/login')">登录</el-button>
            <el-button type="success" size="small" @click="$router.push('/register')">注册</el-button>
          </div>
          <div v-else class="user-dropdown">
            <el-dropdown trigger="click">
              <div class="user-info">
                <img :src="user.avatarUrl" alt="" class="user-avatar">
                <span class="username">{{ user.nickname }}</span>
                <i class="el-icon-arrow-down"></i>
              </div>
              <el-dropdown-menu slot="dropdown">
                <el-dropdown-item v-if="user.role === 'ROLE_ADMIN'">
                  <router-link to="/">后台管理</router-link>
                </el-dropdown-item>
                <el-dropdown-item>
                  <router-link to="/front/password">修改密码</router-link>
                </el-dropdown-item>
                <el-dropdown-item>
                  <router-link to="/front/person">个人信息</router-link>
                </el-dropdown-item>
                <el-dropdown-item>
                  <span @click="logout">退出登录</span>
                </el-dropdown-item>
              </el-dropdown-menu>
            </el-dropdown>
          </div>
        </div>
      </div>
    </header>

    <!-- 内容区域 -->
    <main class="main-content">
      <router-view />
    </main>
  </div>
</template>

<script>
export default {
  name: "Front",
  data() {
    return {
      user: localStorage.getItem("user") ? JSON.parse(localStorage.getItem("user")) : {},
      activeIndex: this.$route.path
    }
  },
  watch: {
    $route(to) {
      this.activeIndex = to.path;
    }
  },
  methods: {
    logout() {
      this.$store.commit("logout")
      this.$message.success("退出成功")
    }
  }
}
</script>

<style>
.front-container {
  min-height: 100vh;
  background-color: #f5f7fa;
}

/* 头部样式 */
.main-header {
  background-color: #fff;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.1);
}

.header-container {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  height: 60px;
  padding: 0 15px;
}

/* Logo样式 */
.logo-container {
  display: flex;
  align-items: center;
  min-width: 150px;
}

.logo-image {
  width: 35px;
  height: 35px;
  margin-right: 8px;
}

.logo-text {
  font-size: 16px;
  font-weight: bold;
  color: #409EFF;
  white-space: nowrap;
}

/* 导航菜单样式 - 使用flex布局确保单行显示 */
.nav-menu {
  display: flex;
  justify-content: center;
  flex: 1;
  overflow-x: auto;
  white-space: nowrap;
  scrollbar-width: none; /* Firefox */
  -ms-overflow-style: none; /* IE and Edge */
}

.nav-menu::-webkit-scrollbar {
  display: none; /* Chrome, Safari, Opera */
}

.nav-item {
  padding: 0 12px;
  line-height: 60px;
  color: #333;
  text-decoration: none;
  font-size: 14px;
  transition: color 0.3s;
}

.nav-item:hover {
  color: #409EFF;
}

.nav-item.active {
  color: #409EFF;
  border-bottom: 2px solid #409EFF;
}

/* 用户区域样式 */
.user-container {
  min-width: 150px;
  text-align: right;
}

.auth-buttons .el-button {
  margin-left: 8px;
}

.user-info {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  cursor: pointer;
}

.user-avatar {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  margin-right: 8px;
}

.username {
  margin-right: 5px;
}

/* 内容区域样式 */
.main-content {
  max-width: 1200px;
  margin: 20px auto;
  padding: 0 20px;
}

/* 确保在小屏幕上也能看到所有菜单项 */
@media (max-width: 1200px) {
  .logo-text {
    display: none;
  }
  
  .user-container {
    min-width: 100px;
  }
  
  .username {
    display: none;
  }
  
  .nav-item {
    padding: 0 8px;
  }
}
</style>
