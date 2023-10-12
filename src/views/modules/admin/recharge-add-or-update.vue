<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="充值金额" prop="price">
      <el-input v-model="dataForm.price" placeholder="充值金额" type="number" clearable></el-input>
    </el-form-item>
    <el-form-item label="赠送金额" prop="givePrice">
      <el-input v-model="dataForm.givePrice" placeholder="赠送金额" type="number" clearable></el-input>
    </el-form-item>
    <el-form-item label="排序" prop="sort">
      <el-input v-model="dataForm.sort" placeholder="排序" type="number"></el-input>
    </el-form-item>
    <p class="formInfo">注：排序数越大越靠前哦~</p>
    <el-form-item label="状态" prop="status">
      <!-- <el-input v-model="dataForm.status" placeholder="状态"></el-input> -->
        <el-radio-group v-model="dataForm.status">
          <el-radio :label="0">有效</el-radio>
          <el-radio :label="1">无效</el-radio>
        </el-radio-group>
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
          price: '',
          givePrice: '',
          sort: '',
          status: ''
        },
        dataRule: {
          price: [
            { required: true, message: '充值金额不能为空', trigger: 'blur' }
          ],
          givePrice: [
            { required: true, message: '赠送金额不能为空', trigger: 'blur' }
          ],
          sort: [
            { required: true, message: '排序不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '状态不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/admin/recharge/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.price = data.recharge.price
                this.dataForm.givePrice = data.recharge.givePrice
                this.dataForm.sort = data.recharge.sort
                this.dataForm.status = data.recharge.status
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
              url: this.$http.adornUrl(`/admin/recharge/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'price': this.dataForm.price,
                'givePrice': this.dataForm.givePrice,
                'sort': this.dataForm.sort,
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
<style lang="scss">
.formInfo {
  line-height: 0px;
  margin-left: 80px;
  color: #eb1111;
}
</style>
