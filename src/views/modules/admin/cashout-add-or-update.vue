<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="提现金额" prop="moneyNumber">
      <el-input v-model="dataForm.moneyNumber" placeholder="申请提现金额" disabled></el-input>
    </el-form-item>
    
    <el-form-item label="审核状态" prop="status">
        <el-radio-group v-model="dataForm.status">
          <el-radio :label="0">待审核</el-radio>
          <el-radio :label="1">已线下打款</el-radio>
          <el-radio :label="2">驳回</el-radio>
        </el-radio-group>
    </el-form-item>
    <el-form-item label="审核反馈" prop="feedback" v-if="dataForm.status==2">
      <el-input v-model="dataForm.feedback" placeholder="请填写原因"></el-input>
    </el-form-item>
    <div v-if="dataForm.status==1" style="color:red;margin-left:30px">请确保已完成线下汇款</div>
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
          moneyNumber: '',
          url: '',
          feedback: '',
          status: '',
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          
          status: [
            { required: true, message: '状态不能为空', trigger: 'blur' }
          ],
          feedback: [
            { required: true, message: '反馈不能为空', trigger: 'blur' }
          ],
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
              url: this.$http.adornUrl(`/admin/cashout/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.uid = data.cashOut.uid
                this.dataForm.moneyNumber = data.cashOut.moneyNumber
                this.dataForm.url = data.cashOut.url
                this.dataForm.feedback = ''
                this.dataForm.status = data.cashOut.status
                this.dataForm.createTime = data.cashOut.createTime
                this.dataForm.updateTime = data.cashOut.updateTime
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
              url: this.$http.adornUrl(`/admin/cashout/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'uid': this.dataForm.uid,
                'moneyNumber': this.dataForm.moneyNumber,
                'url': this.dataForm.url,
                'feedback': this.dataForm.feedback,
                'status': this.dataForm.status,
                'createTime': this.dataForm.createTime,
                'updateTime': this.dataForm.updateTime
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
