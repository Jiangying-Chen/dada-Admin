<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户uid" prop="uid">
      <el-input v-model="dataForm.uid" placeholder="用户uid"></el-input>
    </el-form-item>
    <el-form-item label="关联id" prop="linkId">
      <el-input v-model="dataForm.linkId" placeholder="关联id"></el-input>
    </el-form-item>
    <el-form-item label="0 = 支出 1 = 获得" prop="pm">
      <el-input v-model="dataForm.pm" placeholder="0 = 支出 1 = 获得"></el-input>
    </el-form-item>
    <el-form-item label="账单标题" prop="title">
      <el-input v-model="dataForm.title" placeholder="账单标题"></el-input>
    </el-form-item>
    <el-form-item label="明细种类" prop="category">
      <el-input v-model="dataForm.category" placeholder="明细种类"></el-input>
    </el-form-item>
    <el-form-item label="明细类型" prop="type">
      <el-input v-model="dataForm.type" placeholder="明细类型"></el-input>
    </el-form-item>
    <el-form-item label="明细数字" prop="number">
      <el-input v-model="dataForm.number" placeholder="明细数字"></el-input>
    </el-form-item>
    <el-form-item label="剩余" prop="balance">
      <el-input v-model="dataForm.balance" placeholder="剩余"></el-input>
    </el-form-item>
    <el-form-item label="备注" prop="mark">
      <el-input v-model="dataForm.mark" placeholder="备注"></el-input>
    </el-form-item>
    <el-form-item label="添加时间" prop="addTime">
      <el-input v-model="dataForm.addTime" placeholder="添加时间"></el-input>
    </el-form-item>
    <el-form-item label="0待确定 1有效 -1无效" prop="status">
      <el-input v-model="dataForm.status" placeholder="0待确定 1有效 -1无效"></el-input>
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
          linkId: '',
          pm: '',
          title: '',
          category: '',
          type: '',
          number: '',
          balance: '',
          mark: '',
          addTime: '',
          status: ''
        },
        dataRule: {
          uid: [
            { required: true, message: '用户uid不能为空', trigger: 'blur' }
          ],
          linkId: [
            { required: true, message: '关联id不能为空', trigger: 'blur' }
          ],
          pm: [
            { required: true, message: '0 = 支出 1 = 获得不能为空', trigger: 'blur' }
          ],
          title: [
            { required: true, message: '账单标题不能为空', trigger: 'blur' }
          ],
          category: [
            { required: true, message: '明细种类不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '明细类型不能为空', trigger: 'blur' }
          ],
          number: [
            { required: true, message: '明细数字不能为空', trigger: 'blur' }
          ],
          balance: [
            { required: true, message: '剩余不能为空', trigger: 'blur' }
          ],
          mark: [
            { required: true, message: '备注不能为空', trigger: 'blur' }
          ],
          addTime: [
            { required: true, message: '添加时间不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '0待确定 1有效 -1无效不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/admin/bill/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.uid = data.bill.uid
                this.dataForm.linkId = data.bill.linkId
                this.dataForm.pm = data.bill.pm
                this.dataForm.title = data.bill.title
                this.dataForm.category = data.bill.category
                this.dataForm.type = data.bill.type
                this.dataForm.number = data.bill.number
                this.dataForm.balance = data.bill.balance
                this.dataForm.mark = data.bill.mark
                this.dataForm.addTime = data.bill.addTime
                this.dataForm.status = data.bill.status
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
              url: this.$http.adornUrl(`/admin/bill/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'uid': this.dataForm.uid,
                'linkId': this.dataForm.linkId,
                'pm': this.dataForm.pm,
                'title': this.dataForm.title,
                'category': this.dataForm.category,
                'type': this.dataForm.type,
                'number': this.dataForm.number,
                'balance': this.dataForm.balance,
                'mark': this.dataForm.mark,
                'addTime': this.dataForm.addTime,
                'status': this.dataForm.status
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
