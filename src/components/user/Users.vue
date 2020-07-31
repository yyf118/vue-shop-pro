<template>
  <div>
    <!-- 面包屑导航区域 -->
  <el-breadcrumb separator-class="el-icon-arrow-right">
    <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
    <el-breadcrumb-item>用户管理</el-breadcrumb-item>
    <el-breadcrumb-item>用户列表</el-breadcrumb-item>
  </el-breadcrumb>
<!-- 卡片区域 -->
  <el-card class="box-card">
    <el-row :gutter="20">
      <el-col :span="8">
        <el-input v-model="queryInfo.query" placeholder="请输入内容" class="input-with-select">
          <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
        </el-input>
      </el-col>
      <el-col :span="4">
        <el-button type="primary">添加用户</el-button>
      </el-col>
    </el-row>
    <!-- 用户列表渲染区 -->
    <el-table :data="userList" border stripe>
      <el-table-column label="#" type="index"></el-table-column>
      <el-table-column label="姓名" prop="username"></el-table-column>
      <el-table-column label="邮箱" prop="email"></el-table-column>
      <el-table-column label="电话" prop="mobile"></el-table-column>
      <el-table-column label="角色" prop="role_name"></el-table-column>
      <el-table-column label="状态">
         <template slot-scope="scope">
            <el-switch
              v-model="scope.row.mg_state"
              @change="userStateChanged(scope.row)"
              active-color="#13ce66"
              inactive-color="#ff4949">
            </el-switch>
         </template>
      </el-table-column>
      <el-table-column label="操作" width="180px">
        <template>
          <el-button type="primary" icon="el-icon-edit" size="mini" ></el-button>
        <el-button type="danger" icon="el-icon-delete" size="mini"></el-button>
        <el-button type="warning" icon="el-icon-setting" size="mini"></el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页区域 -->
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="queryInfo.pagenum"
      :page-sizes="[1, 2, 3, 5]"
      :page-size="queryInfo.pagesize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
    </el-pagination>
  </el-card>
  </div>
</template>

<script>
export default {
  data () {
    return {
      queryInfo: {
        query: '',
        pagenum: 1, // 当前的页数
        pagesize: 2 //
      },
      userList: [],
      total: 0
    }
  },
  created () {
    this.getUserList()
  },
  methods: {
    async getUserList (){
      const { data: res } = await this.$http.get('users', {
        params: this.queryInfo
      })
      if (res.meta.status !== 200){
        return this.$message.error('获取用户列表失败！')
      }
      this.userList = res.data.users
      this.total = res.data.total
      console.log(res)
    },
    // 监听pagesize 改变的事件
    handleSizeChange (newSize) {
      this.queryInfo.pagesize = newSize
      this.getUserList()
      // console.log(newSize)
    },
    // 监听 页码值 改变的事件
    handleCurrentChange (newPage){
      this.queryInfo.pagenum = newPage
      this.getUserList()
      // console.log(newPage)
    },
    // 监听switch开关的状态
    async userStateChanged (userInfo){
      // console.log(userInfo)
      const { data: res } = await this.$http.put(`users/${userInfo.id}/state/${userInfo.mg_state}`)
      if (res.meta.status !== 200){
        userInfo.mg_state = !userInfo.mg_state
        return this.$message.error('更新用户状态失败')
      }
      this.$message.success('更新用户状态成功')
    }
  }
}
</script>

<style lang="less" scoped>
</style>
