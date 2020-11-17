<template>
  <div>
    <!-- 面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图 -->
    <el-card>
      <el-row :gutter="20">
        <el-col :span="8">
          <el-input placeholder='请输入内容'>
            <el-button slot='append' icon='el-icon-search'></el-button>
          </el-input>
        </el-col>
        <el-col :span="4">
          <el-button type='primary'>添加商品</el-button>
        </el-col>
      </el-row>

      <el-table
        :data="tableData"
        style="width: 100%"
        border stripe>
        <el-table-column
          type="index"
          label="序号">
        </el-table-column>
        <el-table-column
          prop="date"
          label="商品名称">
        </el-table-column>
        <el-table-column
          prop="name"
          label="商品价格(元)"
          width="120">
        </el-table-column>
        <el-table-column
          prop="address"
          label="商品重量"
          width="120">
        </el-table-column>
        <el-table-column
          prop="address"
          label="创建时间"
          width="180">
        </el-table-column>
        <el-table-column
          label="操作"
          width="150">
          <template slot-scope='scope'>
            <el-button type='primary'>编辑</el-button>
            <el-button type='danger'>删除</el-button>
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
        tableData: []
      }
    },
    created() {

    },
    methods:{

    }
  }
</script>

<style scoped>
</style>
