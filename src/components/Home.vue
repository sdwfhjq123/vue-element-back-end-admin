<template>
  <el-container class="home-container">
    <!-- 头布局 -->
    <el-header>
      <div>
        <img src="~assets/logo.png" alt />
        <span>电商后台管理系统</span>
      </div>
      <el-button type="info" @click="logoutClick">退出</el-button>
    </el-header>
    <!-- 主体布局 -->
    <el-container>
      <!-- 侧边栏布局 -->
      <el-aside :width="isCollapse?'64px':'200px'">
        <!-- 侧边栏菜单区域 -->
        <el-menu
          background-color="#545C64"
          text-color="#fff"
          active-text-color="#409EFF"
          :collapse="isCollapse"
          :collapse-transition="false"
          router
          :default-active="$store.state.activePath"
        >
          <div class="toggle-button" @click="toggleClick">|||</div>
          <!-- 一级菜单 -->
          <el-submenu :index="item.id+''" v-for="item in menuList" :key="item.id">
            <!-- 一级菜单的模板区域 -->
            <template slot="title">
              <!-- 图标 -->
              <i class="el-icon-location"></i>
              <!-- 文本 -->
              <span>{{item.authName}}</span>
            </template>
            <!-- 二级菜单 -->
            <el-menu-item
              :index="'/'+subItem.path"
              v-for="subItem in item.children"
              :key="subItem.id"
              @click="menuItemClick('/'+subItem.path)"
            >
              <template slot="title">
                <!-- 图标 -->
                <i class="el-icon-menu"></i>
                <!-- 文本 -->
                <span>{{subItem.authName}}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <!-- 右侧主体布局 -->
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  name: 'Home',
  data () {
    return {
      menuList: [],
      isCollapse: false
    }
  },
  created () {
    this.getMenuList()
  },
  methods: {
    logoutClick () {
      window.sessionStorage.clear()
      this.$router.replace('/login')
    },
    async getMenuList () {
      const { data: res } = await this.$http.get('menus')
      if (res.meta.status !== 200) return this.$message.error(res.meta.message)
      this.menuList = res.data
    },
    toggleClick () {
      this.isCollapse = !this.isCollapse
    },
    // 点击动态改变default-active颜色值
    menuItemClick (activePath) {
      this.$store.commit('saveNavActivePath', activePath)
    }
  }
}
</script>

<style lang="less" scoped>
.home-container {
  height: 100%;
}
.el-header {
  background-color: #545c64;
  display: flex;
  justify-content: space-between;
  padding-left: 5px;
  align-items: center;
  font-size: 20px;
  color: white;
  > div {
    display: flex;
    align-items: center;
    img {
      width: 50px;
      height: 50px;
      border-radius: 50%;
      background-color: #eee;
    }
    span {
      margin-left: 15px;
    }
  }
}

.el-aside {
  background-color: #545c64;

  .toggle-button {
    background-color: #545c64;
    font-size: 10px;
    line-height: 24px;
    color: white;
    text-align: center;
    letter-spacing: 0.2em;
    cursor: pointer;
  }

  .el-menu {
    border-right: none;
  }
}

.el-main {
  background-color: #eaedf2;
}
</style>
