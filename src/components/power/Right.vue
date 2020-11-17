<template>
  <div>
    <!-- 面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>权限列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图 -->
    <el-card>
      <el-table :data='rigthsList' border stripe>
        <el-table-column label="序号" type="index" width="60"></el-table-column>
        <el-table-column label="权限名称" prop="authName"></el-table-column>
        <el-table-column label="路径" prop="path"></el-table-column>
        <el-table-column label="权限等级" prop="level">
          <template slot-scope='scope'>
            <el-tag v-show="scope.row.level === '0'">一级</el-tag>
            <el-tag v-show="scope.row.level === '1'" type='success'>二级</el-tag>
            <el-tag v-show="scope.row.level === '2'" type='warning'>三级</el-tag>
          </template>
        </el-table-column>
      </el-table>
    </el-card>
  </div>
</template>

<script>
  export default{
    data(){
      return{
        // 权限列表
        rigthsList: [
          {
            id: 101,
            authName: '商品管理',
            level: '0',
            pid: 0,
            path: null
          },
          {
            id: 102,
            authName: '订单管理',
            level: '2',
            pid: 0,
            path: null
          }
        ]
      }
    },
    created() {
      // 获取权限列表
      this.getRightsList()
    },
    methods:{
      // 获取权限列表
      getRightsList(){
        console.log('获取权限列表的请求')
      },
    }
  }
</script>

<style>
</style>
