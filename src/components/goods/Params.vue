<template>
  <div>
    <!-- 面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>分类参数</el-breadcrumb-item>
    </el-breadcrumb>

    <!-- 卡片视图 -->
    <el-card>
      <!-- 警告区 -->
      <el-alert
          title="注意: 只允许为第三级分类设置相关参数!"
          type="warning" :closable="false" show-icon>
      </el-alert>

      <!-- 选择商品分类区域 -->
      <el-row class="cat_opt">
        <el-col>
          <span>选择商品分类：</span>
          <!-- 选择商品分类的级联选择框 -->
          <el-cascader
              v-model="selectedCateKeys"
              :options="catelist"
              :props="cateProps"
              @change="handleChange"></el-cascader>
        </el-col>
      </el-row>
       <!-- Tab 页签区域 -->
      <el-tabs v-model="activeName" @tab-click="handleTabClick">
          <!-- 添加动态参数的面板 -->
          <el-tab-pane label="动态参数" name="first">
            <!-- 添加参数的按钮 -->
            <el-button type='primary' size='mini' :disabled='selectedCateKeys.length === 3 ? false : true'>添加参数</el-button>
          </el-tab-pane>
          <!-- 添加静态属性的面板 -->
          <el-tab-pane label="静态属性" name="second">
            <!-- 添加属性的按钮 -->
            <el-button type='primary' size='mini' :disabled='selectedCateKeys.length === 3 ? false : true'>添加属性</el-button>
          </el-tab-pane>
        </el-tabs>
    </el-card>
  </div>
</template>

<script>
  export default{
    data() {
      return{
        // 商品分类列表
        catelist: [
          {
            cat_id: 1,
            cat_name: '大家电',
            cat_deleted: true,
            cat_level: 0,
            children: [
              {
                cat_id: 11,
                cat_name: '电视',
                cat_deleted: false,
                cat_level: 1,
                children: [
                  {
                    cat_id: 111,
                    cat_name: '曲面电视',
                    cat_deleted: false,
                    cat_level: 2
                  }
                ]
              }
            ]
          }
        ],
        // 级联选择框的配置对象
        cateProps: {
          expandTrigger: 'hover',
          value: 'cat_id',
          label: 'cat_name',
          children: 'children'
        },
        // 级联选择框绑定到的数组
        selectedCateKeys: [],
        // 被激活的页签的名称
        activeName: 'first'
      }
    },
    created() {
      this.getCateList()
    },
    methods:{
      // 获取所有的商品分类列表
      getCateList(){
        // 发送获取所有商品分类的请求

      },
      // 级联选择框选中项变化,会触发这个函数
      handleChange(){
        console.log(this.selectedCateKeys)
      },
      handleTabClick() {
        console.log(this.activeName);
      }
    }
  }
</script>

<style scoped>
  .cat_opt{
    margin: 15px 0;
  }
</style>
