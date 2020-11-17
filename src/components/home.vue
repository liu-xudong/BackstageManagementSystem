<template>
  <el-container class='home_container'>
    <!-- 头部区域 -->
    <el-header>
      <div>
        <img src="../assets/logo.png" alt="">
        <span>电商后台管理系统</span>
      </div>
      <el-button type="into" @click='logout'>退出</el-button>
    </el-header>
    <!-- 页面主体区域 -->
    <el-container>
      <!-- 侧边栏 -->
      <el-aside :width="iscollapse ? '64px' : '200px'">
        <div class="toggle-button" @click="toggleCollapse">|||</div>
        <!-- 侧边栏菜单区域 -->
        <el-menu background-color="#b1b2b3" text-color="#333"
         active-text-color="#fff" unique-opened :collapse='iscollapse'
          :collapse-transition="false" router :default-active="activePath">
          <!-- 一级菜单 -->
          <el-submenu :index="item.id+''" v-for="item in sidebar_list" :key = 'item.id'>
            <!-- 一级菜单的模板区域 -->
            <template slot="title">
              <!-- 图标 -->
              <i :class="iconObj[item.id]"></i>
              <!-- 文本 -->
              <span slot="title">{{item.authName}}</span>
            </template>
            <!-- 二级菜单 -->
            <el-menu-item :index="'/' + subItem.path" v-for="subItem in item.children"
             :key = 'subItem.id' @click="saveNavState('/' + subItem.path)">
              <template slot="title">
                <i class="el-icon-eleme"></i>
                <span slot="title">{{subItem.authName}}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <!-- 右侧内容区域 -->
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
  export default{
    data(){
      return{
        sidebar_list:[
          {
            authName: '用户管理',
            children: [
              {
                authName: '用户列表',
                children: [],
                id: 1011,
                path: 'userlist'
              }
            ],
            id: 101,
            path: 'users'
          },
          {
            authName: '权限管理',
            children: [
              {
                authName: '角色列表',
                children: [],
                id: 1021,
                path: 'rolelist'
              },
              {
                authName: '权限列表',
                children: [],
                id: 1022,
                path: 'rightlist'
              }
            ],
            id: 102,
            path: 'rights'
          },
          {
            authName: '商品管理',
            children: [
              {
                authName: '商品列表',
                children: [],
                id: 1031,
                path: 'goodlist'
              },
              {
                authName: '分类参数',
                children: [],
                id: 1032,
                path: 'sortingParameter'
              },
              {
                authName: '商品分类',
                children: [],
                id: 1033,
                path: 'goodsCategory'
              }
            ],
            id: 103,
            path: 'goods'
          },
          {
            authName: '订单管理',
            children: [
              {
                authName: '订单列表',
                children: [],
                id: 1041,
                path: 'orderlist'
              }
            ],
            id: 104,
            path: 'orders'
          },
          {
            authName: '数据统计',
            children: [],
            id: 105,
            path: 'reports'
          }
        ],
        iconObj:{
          '101': 'el-icon-user',
          '102': 'el-icon-edit',
          '103': 'el-icon-goods',
          '104': 'el-icon-s-order',
          '105': 'el-icon-s-marketing'
        },
        //是否折叠
        iscollapse: false,
        //被激活的链接地址
        activePath: ''
      }
    },
    created() {
      this.activePath = window.sessionStorage.getItem('activePath')
    },
    methods:{
      logout(){
        window.sessionStorage.clear() //清空缓存中的token
        this.$router.push('/login')
      },
      //点击按钮切换菜单的折叠与展开
      toggleCollapse(){
        this.iscollapse = !this.iscollapse
      },
      //保存链接的激活状态
      saveNavState(activePath){
        window.sessionStorage.setItem('activePath', activePath)
        this.activePath = activePath
      }
    }
  }
</script>

<style lang="less" scoped>
  .home_container{
    height: 100%;
  }
  .el-header{
    background-color: #666;
    display: flex;
    justify-content: space-between;
    align-items: center;
    line-height: 60px;
    div{
      display: flex;
      align-items: center;
      color: #FFFFFF;
      img{
        width: 40px;
        height: 40px;
        border-radius: 50%;
      }
    }
    .el-button{
      height: 40px;
    }
  }
  .el-aside{
    background-color: #b1b2b3;
  }
  .el-main{
    background-color: #DCDCDC;
  }
  .el-menu{
    border: none;
  }
  .toggle-button{
    background-color: #123456;
    font-size: 10px;
    line-height: 24px;
    text-align: center;
    letter-spacing: 0.2em;
    color: #FFFFFF;
    cursor: pointer;
  }
</style>
