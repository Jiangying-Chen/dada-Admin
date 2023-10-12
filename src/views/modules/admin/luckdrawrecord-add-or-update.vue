<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户ID" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户ID"></el-input>
    </el-form-item>
    <el-form-item label="奖品ID" prop="prizeId">
      <el-input v-model="dataForm.prizeId" placeholder="奖品ID"></el-input>
    </el-form-item>
    <el-form-item label="奖品类型" prop="prizeType">
      <el-input v-model="dataForm.prizeType" placeholder="奖品类型"></el-input>
    </el-form-item>
    <el-form-item label="奖品名称" prop="prizeName">
      <el-input v-model="dataForm.prizeName" placeholder="奖品名称"></el-input>
    </el-form-item>
    <el-form-item label="奖品图片" prop="prizeImage">
      <el-input v-model="dataForm.prizeImage" placeholder="奖品图片"></el-input>
    </el-form-item>
    <el-form-item label="获得数量" prop="number">
      <el-input v-model="dataForm.number" placeholder="获得数量"></el-input>
    </el-form-item>
    <el-form-item label="抽奖时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="抽奖时间"></el-input>
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
          userId: '',
          prizeId: '',
          prizeType: '',
          prizeName: '',
          prizeImage: '',
          number: '',
          createTime: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户ID不能为空', trigger: 'blur' }
          ],
          prizeId: [
            { required: true, message: '奖品ID不能为空', trigger: 'blur' }
          ],
          prizeType: [
            { required: true, message: '奖品类型不能为空', trigger: 'blur' }
          ],
          prizeName: [
            { required: true, message: '奖品名称不能为空', trigger: 'blur' }
          ],
          prizeImage: [
            { required: true, message: '奖品图片不能为空', trigger: 'blur' }
          ],
          number: [
            { required: true, message: '获得数量不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '抽奖时间不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/admin/luckdrawrecord/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.luckdrawRecord.userId
                this.dataForm.prizeId = data.luckdrawRecord.prizeId
                this.dataForm.prizeType = data.luckdrawRecord.prizeType
                this.dataForm.prizeName = data.luckdrawRecord.prizeName
                this.dataForm.prizeImage = data.luckdrawRecord.prizeImage
                this.dataForm.number = data.luckdrawRecord.number
                this.dataForm.createTime = data.luckdrawRecord.createTime
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
              url: this.$http.adornUrl(`/admin/luckdrawrecord/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'prizeId': this.dataForm.prizeId,
                'prizeType': this.dataForm.prizeType,
                'prizeName': this.dataForm.prizeName,
                'prizeImage': this.dataForm.prizeImage,
                'number': this.dataForm.number,
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
