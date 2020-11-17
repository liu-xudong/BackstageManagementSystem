<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图 -->
    <el-card class="box-card">
      <!-- 搜索与添加区域 -->
      <el-row :gutter="20">
        <el-col :span="7"><el-input placeholder="请输入内容" v-model="inputvalue" @change='search' clearable>
            <el-button slot="append" icon="el-icon-search" @click='search'></el-button>
          </el-input></el-col>
        <el-col :span="4">
          <el-button type="primary" @click='adduser'>添加用户</el-button>
        </el-col>
      </el-row>
      <!-- 用户列表区 -->
      <el-table
            :data="userlist" border stripe>
            <el-table-column type="index"></el-table-column>  //索引列
            <el-table-column
              prop="username"
              label="姓名">
            </el-table-column>
            <el-table-column
              prop="email"
              label="邮箱">
            </el-table-column>
            <el-table-column
              prop="mobile"
              label="电话">
            </el-table-column>
            <el-table-column
              prop="role_name"
              label="角色">
            </el-table-column>
            <el-table-column
              prop="mg_state"
              label="状态">
              <!-- 作用域插槽  使用slot-scope接收作用域的数据，通过scope.row获取当前行的数据-->
              <template slot-scope='scope'>
                <el-switch
                  v-model="scope.row.mg_state">
                </el-switch>
              </template>
            </el-table-column>
            <el-table-column
              label="操作">
              <template slot-scope='scope'>
                <!-- 修改按钮 -->
                <el-button type="primary" icon="el-icon-edit" circle size="mini" @click='showEditDialog(scope.row.id)'></el-button>
                <!-- 删除按钮 -->
                <el-button type="danger" icon="el-icon-delete" circle size="mini" @click='removeUserById(scope.row.id)'></el-button>
                <!-- 分配角色按钮 -->
                <el-tooltip effect="dark" content="分配角色按钮" placement="top" :enterable='false'>
                  <el-button type="warning" icon="el-icon-setting" circle size="mini" @click='setRoles(scope.row)'></el-button>
                </el-tooltip>
              </template>
            </el-table-column>
          </el-table>
          <!-- 分页区域 -->
          <el-pagination
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="currentPage4"
                :page-sizes="[1, 2, 5, 10]"
                :page-size="pagesize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="tableData.length">
              </el-pagination>
    </el-card>

    <!-- 添加用户的对话框 -->
    <el-dialog
      title="添加用户"
      :visible.sync="addDialogVisible"
      width="50%" @close='addclose'>
      <!-- 内容主体区 -->
      <el-form :model="addForm" :rules="addFormrules" ref="addFormRef" label-width="70px">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="addForm.username"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="addForm.password" type='password'></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="addForm.email"></el-input>
        </el-form-item>
        <el-form-item label="手机号" prop="mobile">
          <el-input v-model="addForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <!-- 底部区 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click='addUser'>确 定</el-button>
      </span>
    </el-dialog>

    <!-- 修改用户的对话框 -->
    <el-dialog
      title="修改用户"
      :visible.sync="editDialogVisible"
      width="50%" @close="editclose">
      <!-- 内容主体区 -->
      <el-form :model="editForm" :rules="addFormrules" ref="editFormRef" label-width="70px">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="editForm.username" :disabled="true"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="editForm.email"></el-input>
        </el-form-item>
        <el-form-item label="手机号" prop="mobile">
          <el-input v-model="editForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <!-- 底部区 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="editDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click='editUser'>确 定</el-button>
      </span>
    </el-dialog>

    <!-- 分配角色的对话框 -->
    <el-dialog
      title="设置角色"
      :visible.sync="setRoleDialogVisible"
      width="50%"
      @close="setRoleDialogClosed">
      <!-- 内容主体区 -->
      <div>
        <p>当前的用户： {{userInfo.username}}</p>
        <p>当前的角色： {{userInfo.role_name}}</p>
        <p>分配新角色：
          <el-select v-model="selectedRoleId" placeholder="请选择">
              <el-option
                v-for="item in rolesList"
                :key="item.value"
                :label="item.value"
                :value="item.value">
              </el-option>
            </el-select>
        </p>
      </div>
      <!-- 底部区 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="setRoleDialogVisible = false">取 消</el-button>
        <el-button type="primary" @click='saveRoleInfo'>确 定</el-button>
      </span>
    </el-dialog>


  </div>
</template>

<script>
  export default{
    data(){
      // 验证邮箱的规则
      var checkEmail = (rule,value,cb) => {
        // 验证邮箱的正则
        const regEmail = /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(\.[a-zA-Z0-9_-])+/
        if(regEmail.test(value)){
          // 合法邮箱
          return cb()
        }

        cb(new Error('请输入合法的邮箱'))
      }
      // 验证手机号的规则
      var checkMobile = (rule,value,cb) => {
        // 验证手机号的正则
        const regMobile = /^(0|86|17951)?(13[0-9]|15[012356789]|17[678]|18[0-9]|14[57])[0-9]{8}$/

        if(regMobile.test(value)){
          return cb()
        }

        cb(new Error('请输入合法的手机号'))
      }
      return{
        // 已选中的角色数据
        selectedRoleId: '',
        // 所有角色的数据列表
        rolesList: [
          {value: '主管'},
          {value: '测试'},
          {value: '测试角色'},
          {value: 'test'},
          {value: 'dsdf'},
          {value: '管理员1'},
          {value: '管理员2'},
          {value: '管理员3'},
          {value: '管理员4'}
        ],
        // 需要被分配角色的用户信息
        userInfo: {},
        // 控制分配角色的显示于隐藏
        setRoleDialogVisible: false,
        tableData: [{
          id: 23,
          username: 'liu',
          mobile: '17609200438',
          type: 1,
          email: 'z17609200438@163.com',
          create_time: '',
          mg_state: true,
          role_name: '管理员1'
        },{
          id: 24,
          username: 'xu',
          mobile: '17609200438',
          type: 1,
          email: 'z17609200438@163.com',
          create_time: '',
          mg_state: false,
          role_name: '管理员2'
        },{
          id: 25,
          username: 'dong',
          mobile: '17609200438',
          type: 1,
          email: 'z17609200438@163.com',
          create_time: '',
          mg_state: true,
          role_name: '管理员3'
        },{
          id: 26,
          username: 'lxd',
          mobile: '17609200438',
          type: 1,
          email: 'z17609200438@163.com',
          create_time: '',
          mg_state: true,
          role_name: '管理员4'
        }],
        // 当前页数
        currentPage4: 0,
        // 当前每页显示多少条数据
        pagesize: 5,
        // 显示的用户列表
        userlist:[],
        // input输入框内容
        inputvalue:'',
        // 控制添加用户对话框的显示与隐藏
        addDialogVisible:false,
        // 添加用户的表单数据
        addForm:{
          username: '',
          password: '',
          email: '',
          mobile: ''
        },
        // 添加表单的验证规则对象
        addFormrules:{
          username:[{
            required: true,message:'请输入用户名',trigger:'blur'
          },{
            min: 3, max: 10,message:'用户名的长度在3~10个字符之间',trigger:'blur'
          }],
          password:[{
            required: true,message:'请输入密码',trigger:'blur'
          },{
            min: 6, max: 15,message:'密码的长度在6~15个字符之间',trigger:'blur'
          }],
          email:[{
            required: true,message:'请输入邮箱',trigger:'blur'
          },{
            validator: checkEmail, trigger: 'blur'
          }],
          mobile:[{
            required: true,message:'请输入手机',trigger:'blur'
          },{
            min: 11, max: 11,message:'手机号长度为11位',trigger:'blur'
          },{
            validator: checkMobile, trigger: 'blur'
          }]
        },
        // 控制修改用户对话框的显示和隐藏
        editDialogVisible: false,
        editForm: {
          username: '',
          email: '',
          mobile: ''
        }
      }
    },
    created() {
      this.getUserList()
      this.currentPage4 = this.tableData.length / this.pagesize
    },
    methods:{
      getUserList(){
        console.log('发送请求')
        // this.$http.get('users',{}).then((res)=>{
              console.log('做请求成功操作')
        // }).catch((e)=>{
        //   console.log(e)
        // })
        this.userlist = this.tableData.slice(0,this.pagesize)
      },
      // 监听 pagesize 改变的事件
      handleSizeChange(newSize){
        this.pagesize = newSize
        this.userlist = this.tableData.slice(0,this.pagesize)
      },
      // 监听页码值改变的监听事件
      handleCurrentChange(newCurrent){
        this.userlist = this.tableData.slice(this.pagesize*(newCurrent-1),this.pagesize*newCurrent)
      },
      // 搜索功能
      search(){
          this.userlist = []
          for(let i = 0;i < this.tableData.length;i++){
            let pos = this.tableData[i].username.indexOf(this.inputvalue)
            if(pos !== -1){
              this.userlist.push(this.tableData[i])
            }
          }
      },
      // 添加用户回调
      adduser(){
        this.addDialogVisible = true
      },
      // 关闭添加用户对话框回调
      addclose(){
        this.$refs.addFormRef.resetFields()
      },
      // 点击按钮添加用户
      addUser(){
        this.$refs.addFormRef.validate(valid => {
          if(!valid) return
          // 可以发起添加用户的网络请求

          this.addDialogVisible = false
        })
      },
      // 展示编辑用户的对话框
      showEditDialog(id){
        for(let i = 0;i < this.tableData.length;i++){
          if(id === this.tableData[i].id){
            this.editForm.username = this.tableData[i].username
            this.editForm.email = this.tableData[i].email
            this.editForm.mobile = this.tableData[i].mobile
            break
          }
        }
        this.editDialogVisible = true
      },
      // 关闭修改用户信息对话框回调
      editclose(){
        this.$refs.editFormRef.resetFields()
      },
      // 点击按钮修改用户信息
      editUser(){
        this.$refs.editFormRef.validate(valid => {
          if(!valid) return
          // 可以发起添加用户的网络请求
          this.editDialogVisible = false
        })
      },
      // 根据ID删除对应的用户信息
      removeUserById(id){
        // 弹框询问用户是否删除用户信息
        const res = this.$confirm('此操作将永久删除该用户, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(res=>{
          if(res === 'confirm'){
            console.log('发送请求删除')
          }
        }).catch(err=>err)
        // 如果用户确认删除,则返回值为字符串 confirm
        // 如果用户取消删除,则返回值为字符串 cancel
      },
      // 展示分配角色的对话框
      setRoles(row){
        this.userInfo = row
        // 获取所有角色的数据列表（请求）

        this.setRoleDialogVisible = true
      },
      // 点击按钮,分配角色
      saveRoleInfo(){
        if(!this.selectedRoleId){
          return this.$message.error('请选择要分配的角色')
        }

        // 发送修改角色的请求
      },
      // 监听分配角色对话框的关闭
      setRoleDialogClosed(){
        this.selectedRoleId = ''
        this.userInfo = {}
      }
    }
  }
</script>

<style lang="less" scoped>

</style>
