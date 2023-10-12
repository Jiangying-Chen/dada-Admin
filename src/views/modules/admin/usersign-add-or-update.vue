<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户id" prop="uid">
      <el-input v-model="dataForm.uid" placeholder="用户id"></el-input>
    </el-form-item>
    <el-form-item label="签到说明" prop="title">
      <el-input v-model="dataForm.title" placeholder="签到说明"></el-input>
    </el-form-item>
    <el-form-item label="获得积分" prop="number">
      <el-input v-model="dataForm.number" placeholder="获得积分"></el-input>
    </el-form-item>
    <el-form-item label="剩余积分" prop="balance">
      <el-input v-model="dataForm.balance" placeholder="剩余积分"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          uid: '',
          title: '',
          number: '',
          balance: '',
          createTime: ''
        },
        dataRule: {
          uid: [
            { required: true, message: '用户id不能为空', trigger: 'blur' }
          ],
          title: [
            { required: true, message: '签到说明不能为空', trigger: 'blur' }
          ],
          number: [
            { required: true, message: '获得积分不能为空', trigger: 'blur' }
          ],
          balance: [
            { required: true, message: '剩余积分不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/admin/usersign/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.uid = data.userSign.uid
                this.dataForm.title = data.userSign.title
                this.dataForm.number = data.userSign.number
                this.dataForm.balance = data.userSign.balance
                this.dataForm.createTime = data.userSign.createTime
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/admin/usersign/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'uid': this.dataForm.uid,
                'title': this.dataForm.title,
                'number': this.dataForm.number,
                'balance': this.dataForm.balance,
                'createTime': this.dataForm.createTime
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
