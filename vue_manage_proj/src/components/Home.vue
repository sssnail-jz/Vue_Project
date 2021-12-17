<template>
  <el-container>
    <el-header></el-header>
    <el-container>
      <el-aside :width="isCollapse ? '64px' : '200px'">
        <div class="toggle-button" @click="onCollapseMenu">|||</div>
        <!-- 菜单容器 -->
        <el-menu
          active-text-color="#ffd04b"
          background-color="#333744"
          text-color="#fff"
          :collapse="isCollapse"
          :collapse-transition="false"
          :router="true"
          :default-active="activePath"
        >
          <!-- 一级菜单 -->
          <el-submenu
            :index="item.id + ''"
            v-for="item in menulist"
            :key="item.id"
          >
            <!-- 一级菜单文本 -->
            <template #title>
              <el-icon><location /></el-icon>
              <span>{{ item.authName }}</span>
            </template>
            <!-- 二级菜单 -->
            <el-menu-item
              :index="'/' + subitem.path"
              v-for="subitem in item.children"
              :key="subitem.id"
              @click="onSetActivePath('/' + subitem.path)"
            >
              <!-- 二级菜单文本 -->
              <template #title>
                <el-icon><location /></el-icon>
                <span>{{ subitem.authName }}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  data () {
    return {
      menulist: [],
      isCollapse: false,
      activePath: ''
    }
  },
  created () {
    this.onGetMenuList()
    this.activePath = window.sessionStorage.getItem('activePath')
  },
  methods: {
    onLogOut () {},
    async onGetMenuList () {
      const { data: res } = await this.$http.get('menus')
      if (res.meta.status !== 200) {
        return this.$message.error(res.meta.msg)
      } else {
        this.menulist = res.data
        console.log(res.data)
      }
    },
    onCollapseMenu () {
      this.isCollapse = !this.isCollapse
    },
    onSetActivePath (activePath) {
      window.sessionStorage.setItem('activePath', activePath)
      this.activePath = activePath
    }
  }
}
</script>

<style lang="less" scoped>
.el-container {
  height: 100%;
}
.el-header {
  background-color: #373d41;
}
.el-aside {
  background-color: #333744;
}
.el-main {
  background-color: #eaedf1;
}
.toggle-button {
  color: white;
  text-align: center;
}
</style>
