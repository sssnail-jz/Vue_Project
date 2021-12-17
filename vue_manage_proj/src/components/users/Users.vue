<template>
  <el-card class="box-card">
    <template #header>
      <div class="card-header">
        <span>Card name</span>
        <el-button class="button" type="text" @click="onAddUser"
          >添加用户</el-button
        >
      </div>
    </template>
    <el-table :data="userList" stripe style="width: 100%" border>
      <el-table-column type="index" />
      <el-table-column prop="username" label="用户名" width="180" />
      <el-table-column prop="email" label="邮箱" width="180" />
      <el-table-column prop="mobile" label="电话" />
      <el-table-column prop="role_name" label="角色">
        <!-- 作用域插槽拿到此行对应的数据 -->
        <template slot-scope="scope">
          <el-switch
            v-model="scope.row.mg_state"
            @change="onUserStateChange(scope.row)"
          ></el-switch>
        </template>
      </el-table-column>
      <el-table-column prop="mg_state" label="状态" />
      <el-table-column label="操作" />
    </el-table>
    <el-pagination
      :current-page="queryInfo.pagenum"
      :page-sizes="[2, 5, 10, 15]"
      :page-size="queryInfo.pagesize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total"
      @size-change="onHandleSizeChange"
      @current-change="onHandleCurrentChange"
    >
    </el-pagination>
    <el-dialog title="Tips" width="50%" :visible.sync="addDialogVisible">
      <el-form :modal="addForm" label-width="70px" ref="addFormRef">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="addForm.username"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="addForm.password"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="addForm.email"></el-input>
        </el-form-item>
        <el-form-item label="电话" prop="mobile">
          <el-input v-model="addForm.mobile"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible = false">Cancel</el-button>
        <el-button type="primary" @click="onAddUserClicked">Confirm</el-button>
      </span>
    </el-dialog>
  </el-card>
</template>

<script>
export default {
  data () {
    return {
      queryInfo: {
        query: '',
        pagenum: 1,
        pagesize: 2
      },
      userList: [],
      total: 0,
      addDialogVisible: false,
      addForm: {
        username: 'admin1',
        password: '123456',
        email: 'admin1@qq.com',
        mobile: '1234567890'
      }
    }
  },
  created () {
    this.onGetUserList()
  },
  methods: {
    async onGetUserList () {
      const { data: res } = await this.$http.get('users', {
        params: this.queryInfo
      })
      if (res.meta.status !== 200) {
        return this.$message.error('获取用户列表失败')
      } else {
        this.userList = res.data.users
        this.total = res.data.total
        console.log(res)
      }
    },
    onHandleSizeChange (newSize) {
      console.log(newSize)
      this.queryInfo.pagesize = newSize
      this.onGetUserList()
    },
    onHandleCurrentChange (newCurPage) {
      console.log(newCurPage)
      this.queryInfo.pagenum = newCurPage
      this.onGetUserList()
    },
    async onUserStateChange (row) {
      const { data: res } = await this.$http.put(
        `users/${row.id}/state/${row.mg_state}`
      )
      if (res.meta.status !== 200) {
        row.mg_state = !row.mg_state
        return this.$message.error('修改用户状态失败')
      }
      this.$message.success('修改用户状态成功')
    },
    onAddUser () {
      this.addDialogVisible = true
    },
    async onAddUserClicked () {
      // this.$refs.addFormRef.validate(async (validate) => {
      //   if (!validate) {
      //     return this.$message.error('信息有误')
      //   } else {

      //   }
      // })
      const { data: res } = await this.$http.post('users', this.addForm)
      if (res.meta.status !== 201) {
        return this.$message.error('添加用户失败')
      } else {
        this.$message.success('添加用户成功')
        this.addDialogVisible = false
        this.onGetUserList()
      }
    }
  }
}
</script>

<style lang="less" scoped></style>
