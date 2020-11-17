<template>
  <div>
    <!-- 面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品分类</el-breadcrumb-item>
    </el-breadcrumb>

    <!-- 卡片视图 -->
    <el-card>
      <el-row>
        <el-col>
          <el-button type='primary' @click='addCate'>添加分类</el-button>
        </el-col>
      </el-row>

      <!-- 表格 -->
      <tree-table class="treeTable" :data='cateList' :columns='columns' :selection-type='false'
      :expand-type='false' show-index border :show-row-hover='false'>
        <template slot='isok' slot-scope='scope'>
          <i v-show="!scope.row.cat_deleted" class="el-icon-success"
          style="color: lightgreen;"></i>
          <i v-show="scope.row.cat_deleted" class="el-icon-error"
          style="color: red;"></i>
        </template>
        <template slot='order' slot-scope='scope'>
          <el-tag v-show="scope.row.cat_level === 0" size='mini'>一级</el-tag>
          <el-tag v-show="scope.row.cat_level === 1" type='success' size='mini'>二级</el-tag>
          <el-tag v-show="scope.row.cat_level === 2" type='warning' size='mini'>三级</el-tag>
        </template>
        <template slot='opt' slot-scope='scope'>
          <el-button type='primary' icon='el-icon-edit' size='mini'>编辑</el-button>
          <el-button type='danger' icon='el-icon-delete' size='mini'>删除</el-button>
        </template>
      </tree-table>

      <!-- 分页 -->
      <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="querInfo.pagenum"
            :page-sizes="[3, 5, 10, 15]"
            :page-size="querInfo.pagesize"
            layout="total, sizes, prev, pager, next, jumper"
            :total="total">
          </el-pagination>
    </el-card>

    <el-dialog
      title="添加分类"
      :visible.sync="addCateDialogVisible"
      width="50%"
      @close="addCateDialogClosed">
      <!-- 添加分类表单 -->
      <el-form :model="addCateForm" :rules="addCateFormRules" ref="addCateFormRef" label-width="100px">
        <el-form-item label="分类名称" prop="cat_name">
          <el-input v-model="addCateForm.cat_name"></el-input>
        </el-form-item>
        <el-form-item label="父级分类">
          <!-- options 用来指定数据源 -->
          <!-- props 用来指定配置对象 -->
          <el-cascader
              v-model="selectedKeys"
              :options="parentCateList"
              :props="cascaderProps"
              @change="parentCateChanged" clearable
              change-on-select></el-cascader>
        </el-form-item>
      </el-form>
      <!-- 底部区 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="addCateDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click='addCateDialogVisible = false'>确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
  export default{
    data() {
      return {
        // 查询条件
        querInfo: {
          type: 3,
          pagenum: 1,
          pagesize: 5
        },
        // 商品分类的数据列表
        cateList: [
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
        total: 0,
        columns:[
          {
            label: '分类名称',
            prop: 'cat_name'
          },
          {
            label: '是否有效',
            // 表示当前列定义为模板列
            type: 'template',
            // 表示当前这一列使用的模板名称
            template: 'isok',
            prop: 'cat_deleted'
          },
          {
            label: '排序',
            // 表示当前列定义为模板列
            type: 'template',
            // 表示当前这一列使用的模板名称
            template: 'order',
          },
          {
            label: '操作',
            // 表示当前列定义为模板列
            type: 'template',
            // 表示当前这一列使用的模板名称
            template: 'opt',
          }
        ],
        // 控制添加分类对话框的显示和隐藏
        addCateDialogVisible: false,
        // 添加分类的表单数据对象
        addCateForm: {
          // 将要添加分类的名称
          cat_name: '',
          // 父级分类的Id
          cat_pid: 0,
          // 分类的等级默认要添加的是一级分类
          cat_level: 0
        },
        // 添加分类表单的验证规则
        addCateFormRules: {
          cat_name: [
            { required: true, message: '请输入分类的名称', trigger: 'blur'}
          ]
        },
        // 父级分类的列表
        parentCateList: [],
        // 指定级联选择器的配置对象
        cascaderProps: {
          expandTrigger: 'hover',
          value: 'cat_id',
          label: 'cat_name',
          children: 'children'
        },
        // 选中的父级分类的Id数组
        selectedKeys: []
      }
    },
    created() {
      this.getCateList()
    },
    methods:{
      // 获取商品分类数据
      getCateList(){
        // 请求商品分类数据
        this.total = this.cateList.length
      },
      // 监听 pagesize 改变
      handleSizeChange(newSize){
        this.querInfo.pagesize = newSize
        this.getCateList()
      },
      // 监听 pagenum 改变
      handleCurrentChange(newNum){
        this.querInfo.pagenum = newNum
        this.getCateList()
      },
      // 点击按钮,展示添加分类的对话框
      addCate(){
        // 先获取父级分类的数据列表 再显示对话框

        this.getParentCateList()

        this.addCateDialogVisible = true
      },
      // 获取父级分类的数据列表
      getParentCateList(){
        // 发送获取父级分类的请求
        this.parentCateList = this.cateList

      },
      // 选择项发生变化出发这个函数
      parentCateChanged(){
        console.log(this.selectedKeys)
      },
      // 监听添加分类对话框的关闭
      addCateDialogClosed(){
        this.$refs.addCateFormRef.resetFields()
        this.selectedKeys = []
      }
    }
  }
</script>

<style scoped>
  .treeTable{
    margin-top: 15px;
  }
  .el-cascader{
    width: 100%;
  }
</style>
