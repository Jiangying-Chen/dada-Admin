<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="名称" prop="name">
      <el-input v-model="dataForm.name" placeholder="名称"></el-input>
    </el-form-item>
    <el-form-item label="有效天数" prop="validDays" >
      <el-input v-model="dataForm.validDays" placeholder="有效天数" type="number"></el-input>
    </el-form-item>
    <el-form-item label="价格" prop="price" >
      <el-input v-model="dataForm.price" placeholder="价格" type="number"></el-input>
    </el-form-item>
    <el-form-item label="描述" prop="remark">
      <el-input v-model="dataForm.remark" placeholder="描述"></el-input>
    </el-form-item>
    <el-form-item label="排序" prop="sort" >
      <el-input v-model="dataForm.sort" placeholder="排序" type="number"></el-input>
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
          name: '',
          validDays: '',
          price: '',
          remark: '',
          sort: ''
        },
        dataRule: {
          name: [
            { required: true, message: '名称不能为空', trigger: 'blur' }
          ],
          validDays: [
            { required: true, message: '有效天数不能为空', trigger: 'blur' }
          ],
          price: [
            { required: true, message: '价格不能为空', trigger: 'blur' }
          ],
          remark: [
            { required: true, message: '描述不能为空', trigger: 'blur' }
          ],
          sort: [
            { required: true, message: '排序不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/admin/vipoption/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.name = data.vipOption.name
                this.dataForm.validDays = data.vipOption.validDays
                this.dataForm.price = data.vipOption.price
                this.dataForm.remark = data.vipOption.remark
                this.dataForm.sort = data.vipOption.sort
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
              url: this.$http.adornUrl(`/admin/vipoption/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'name': this.dataForm.name,
                'validDays': this.dataForm.validDays,
                'price': this.dataForm.price,
                'remark': this.dataForm.remark,
                'sort': this.dataForm.sort
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
