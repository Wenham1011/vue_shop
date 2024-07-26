<script>
export default {
  data() {
    return {
      menuList: [],
      iconObj: {
        '125': 'el-icon-user-solid',
        '103': 'el-icon-s-tools',
        '101': 'el-icon-shopping-cart-full',
        '102': 'el-icon-tickets',
        '145': 'el-icon-s-data'
      },
      isCollapse: false
      // activePath: ''
    }
  },
  created() {
    this.getMenuList()
    // this.activePath = window.sessionStorage.getItem('activePath')
  },
  methods: {
    logout() {
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    async getMenuList() {
      const {data: res} = await this.$http.get('menus')
      if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
      this.menuList = res.data
      // console.log(res)
    },
    toggleClick() {
      this.isCollapse = !this.isCollapse
    }
    // saveNavState(activePath) {
    //   window.sessionStorage.setItem('activePath', activePath)
    //   this.activePath = activePath
    // }
  }
}
</script>

<template>
  <el-container class="home-container">
    <!--    头部区域-->
    <el-header>
      <div>
        <img src="../assets/kenan.png" alt="电商管理系统头像">
        <span>电商后台管理系统</span>
      </div>
      <el-button type="info" @click="logout">退出</el-button>
    </el-header>
    <!--    页面主体区域-->
    <el-container>
      <!--      侧边栏区域-->
      <el-aside :width="isCollapse ? '64px' : '200px'">
        <div class="toggle-button" @click="toggleClick">| | |</div>
        <el-menu background-color="rgb(55,55,68)" text-color="#fff" active-text-color="#8470FF" :collapse="isCollapse" :collapse-transition="false" :router="true" :default-active="this.$router.currentRoute.path">
          <!--          一级菜单区域-->
          <el-submenu :index="item.id + ''" v-for="item in menuList" :key="item.id">
            <!--            一级菜单模版区域-->
            <template slot="title">
              <i :class="iconObj[item.id]"></i>
              <span>{{item.authName}}</span>
            </template>
            <!--            二级菜单-->
            <el-menu-item :index="'/' + subItem.path" v-for="subItem in item.children" :key="subItem.id">
              <template slot="title">
                <i class="el-icon-menu"></i>
                <span>{{subItem.authName}}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <!--      右侧区域-->
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<style lang="less" scoped>
.home-container {
  height: 100%;
}

.el-header {
  background: #373d41;
  display: flex;
  justify-content: space-between;
  padding-left: 0;
  align-items: center;

  > div {
    display: flex;
    align-items: center;
  }

  span {
    color: white;
    font-size: 20px;
    margin-left: 10px;
  }

  img {
    height: 100%;
    width: 60px;
    border: 1px solid #373d80;
    border-radius: 50%;
  }
}

.el-aside {
  background: #333744;
  .el-menu{
    border-right: none;
  }
}

.el-main {
  background: #eaedf1;
}

.toggle-button{
  background: #4a5064;
  font-size: 10px;
  line-height: 20px;
  color: white;
  text-align: center;
  cursor: pointer;
}
</style>
