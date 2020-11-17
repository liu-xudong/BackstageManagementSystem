<template>
  <div>
    <!-- 面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>权限管理</el-breadcrumb-item>
      <el-breadcrumb-item>角色列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图 -->
    <el-card>
      <el-row>
        <el-col>
          <el-button type='primary'>添加角色</el-button>
        </el-col>
      </el-row>
      <el-table :data='rolesList' border stripe>
        <!-- 展开列 -->
        <el-table-column type="expand">
          <template slot-scope='scope'>
            <el-row v-for="(item1,i1) in scope.row.children" :key='item1.id' :class="['bdbottom',i1 === 0 ? 'bdtop' : '','vcenter']">
              <!-- 渲染一级权限 -->
              <el-col :span="5">
                <el-tag closable @close='removeRightByID()'>{{item1.value}}</el-tag>
                <i class="el-icon-caret-right"></i>
              </el-col>
              <!-- 渲染二级和三级权限 -->
              <el-col :span="19">
                <!-- 通过 for 循环 嵌套渲染二级权限 -->
                <el-row v-for="(item2, i2) in item1.children" :key='item2.id' :class="[i2 === item1.children.length-1 ? '' : 'bdbottom','vcenter']">
                  <el-col :span="6">
                    <el-tag closable @close='removeRightByID()' type='success'>{{item2.value}}</el-tag>
                    <i class="el-icon-caret-right"></i>
                  </el-col>
                  <el-col :span="18">
                    <el-tag closable @close='removeRightByID()' type='warning' v-for="(item3, i3) in item2.children"
                      :key='item3.id'>{{item3.value}}</el-tag>
                  </el-col>
                </el-row>
              </el-col>
            </el-row>

            <!-- 展示数据 -->
            <!-- <pre>
              {{scope.row}}
            </pre> -->
          </template>
        </el-table-column>
        <!-- 索引列 -->
        <el-table-column label="序号" type="index" width="60"></el-table-column>
        <el-table-column label="角色名称" prop="value"></el-table-column>
        <el-table-column label="角色描述" prop="value"></el-table-column>
        <el-table-column label="操作">
          <template slot-scope='scope'>
            <el-button type='primary' icon='el-icon-edit' size='mini'>编辑</el-button>
            <el-button type='danger' icon='el-icon-delete' size='mini'>删除</el-button>
            <el-button type='warning' icon='el-icon-setting' size='mini' @click='showSetRightDial(scope.row)'
            >分配权限</el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-card>
    <el-dialog
      title="分配权限"
      :visible.sync="setRightDialogVisible"
      width="50%"
      @close="setRightDialogClosed">
      <!-- 树形控件 -->
      <el-tree :data="rolesList[0].children" ref='treeRef' :props="treeProps" :default-checked-keys='defKeys' show-checkbox default-expand-all node-key="id"></el-tree>
      <span slot="footer" class="dialog-footer">
        <el-button @click="setRightDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="allotRights">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        // 即将分配权限的角色Id
        roleId: '',
        // 默认选中的节点Id值数组
        defKeys: [],
        // 树形控件的属性绑定事件
        treeProps: {
          // 父子节点嵌套字段
          children: 'children',
          // 展示的内容字段
          label: 'value'
        },
        // 控制分配权限对话框的现实和隐藏
        setRightDialogVisible: false,
        // 角色列表
        rolesList: [{
          id: 3,
          value: '主管',
          children: [{
              id: 11,
              value: '一级权限',
              children: [{
                  id: 111,
                  value: '二级权限',
                  children: [{
                      id: 1111,
                      value: '三级权限'
                    },
                    {
                      id: 1112,
                      value: '三级权限'
                    },
                    {
                      id: 1113,
                      value: '三级权限'
                    },
                    {
                      id: 1114,
                      value: '三级权限'
                    },
                    {
                      id: 1115,
                      value: '三级权限'
                    },
                    {
                      id: 1116,
                      value: '三级权限'
                    },
                    {
                      id: 1117,
                      value: '三级权限'
                    },
                    {
                      id: 1118,
                      value: '三级权限'
                    }
                  ]
                },
                {
                  id: 112,
                  value: '二级权限',
                  children: [{
                      id: 1121,
                      value: '三级权限'
                    },
                    {
                      id: 1122,
                      value: '三级权限'
                    },
                    {
                      id: 1123,
                      value: '三级权限'
                    }
                  ]
                },
                {
                  id: 113,
                  value: '二级权限',
                  children: [{
                      id: 1131,
                      value: '三级权限'
                    },
                    {
                      id: 1132,
                      value: '三级权限'
                    },
                    {
                      id: 1133,
                      value: '三级权限'
                    }
                  ]
                }
              ]
            },
            {
              id: 12,
              value: '一级权限',
              children: [{
                  id: 121,
                  value: '二级权限',
                  children: [{
                      id: 1211,
                      value: '三级权限'
                    },
                    {
                      id: 1212,
                      value: '三级权限'
                    },
                    {
                      id: 1213,
                      value: '三级权限'
                    }
                  ]
                },
                {
                  id: 122,
                  value: '二级权限',
                  children: [{
                      id: 1221,
                      value: '三级权限'
                    },
                    {
                      id: 1222,
                      value: '三级权限'
                    },
                    {
                      id: 1223,
                      value: '三级权限'
                    }
                  ]
                },
                {
                  id: 123,
                  value: '二级权限',
                  children: [{
                      id: 1231,
                      value: '三级权限'
                    },
                    {
                      id: 1232,
                      value: '三级权限'
                    },
                    {
                      id: 1233,
                      value: '三级权限'
                    }
                  ]
                }
              ]
            },
            {
              id: 13,
              value: '一级权限',
              children: [{
                  id: 131,
                  value: '二级权限',
                  children: [{
                      id: 1311,
                      value: '三级权限'
                    },
                    {
                      id: 1312,
                      value: '三级权限'
                    },
                    {
                      id: 1313,
                      value: '三级权限'
                    }
                  ]
                },
                {
                  id: 132,
                  value: '二级权限',
                  children: [{
                      id: 1321,
                      value: '三级权限'
                    },
                    {
                      id: 1322,
                      value: '三级权限'
                    },
                    {
                      id: 1323,
                      value: '三级权限'
                    }
                  ]
                },
                {
                  id: 133,
                  value: '二级权限',
                  children: [{
                      id: 1331,
                      value: '三级权限'
                    },
                    {
                      id: 1332,
                      value: '三级权限'
                    },
                    {
                      id: 1333,
                      value: '三级权限'
                    }
                  ]
                }
              ]
            }

          ]

        }]
      }
    },
    created() {
      // 获取角色列表
      this.getRolesList()
    },
    methods: {
      // 获取角色列表
      getRolesList() {
        console.log('获取角色列表的请求')
      },
      // 根据 Id 删除对应的权限
      removeRightByID() {
        // 弹框提示用户是否要删除
        this.$confirm('此操作将永久删除该文件，是否继续？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then((confirmResult) => {
          if(confirmResult === 'confirm'){
            console.log('发送删除请求')
            // 删除的接口返回删除后的数据信息,重新把数据赋值就可以不刷新页面了
          }
        }).catch(err => err)
      },
      // 展示分配权限的对话框
      showSetRightDial(role){
        this.roleId = role.id
        // 获取三级节点的Id
        this.getLeafKeys(this.rolesList[0],this.defKeys)

        this.setRightDialogVisible = true
      },
      // 通过递归的形式,获取角色下所有三级权限的Id,并保存到 defKeys 数组中
      getLeafKeys(node, arr){
        // 如果当前 node 节点不包含 children 属性,则是三级节点
        if(!node.children) {
          return arr.push(node.id)
        }

        node.children.forEach(item => this.getLeafKeys(item, arr))
      },
      // 监听分配权限对话框的关闭事件
      setRightDialogClosed(){
        this.defKeys = []
      },
      // 点击为角色分配权限
      allotRights(){
        const keys = [
          ...this.$refs.treeRef.getCheckedKeys(),  // ... 展开运算符
          ...this.$refs.treeRef.getHalfCheckedKeys()
        ]
        const idStr = keys.join(',')
        console.log(idStr,this.roleId)
      }
    }
  }
</script>

<style scoped>
  .el-tag {
    margin: 7px;
  }

  .bdtop {
    border-top: 1px solid #eee;
  }

  .bdbottom {
    border-bottom: 1px solid #eee;
  }

  .vcenter {
    display: flex;
    align-items: center;
  }
</style>
