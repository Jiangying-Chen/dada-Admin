<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="充值用户UID" prop="uid">
      <el-input v-model="dataForm.uid" placeholder="充值用户UID"></el-input>
    </el-form-item>
    <el-form-item label="" prop="nickname">
      <el-input v-model="dataForm.nickname" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="订单号" prop="orderId">
      <el-input v-model="dataForm.orderId" placeholder="订单号"></el-input>
    </el-form-item>
    <el-form-item label="充值金额" prop="price">
      <el-input v-model="dataForm.price" placeholder="充值金额"></el-input>
    </el-form-item>
    <el-form-item label="购买赠送金额" prop="givePrice">
      <el-input v-model="dataForm.givePrice" placeholder="购买赠送金额"></el-input>
    </el-form-item>
    <el-form-item label="充值类型" prop="rechargeType">
      <el-input v-model="dataForm.rechargeType" placeholder="充值类型"></el-input>
    </el-form-item>
    <el-form-item label="是否充值" prop="paid">
      <el-input v-model="dataForm.paid" placeholder="是否充值"></el-input>
    </el-form-item>
    <el-form-item label="充值支付时间" prop="payTime">
      <el-input v-model="dataForm.payTime" placeholder="充值支付时间"></el-input>
    </el-form-item>
    <el-form-item label="充值时间" prop="addTime">
      <el-input v-model="dataForm.addTime" placeholder="充值时间"></el-input>
    </el-form-item>
    <el-form-item label="退款金额" prop="refundPrice">
      <el-input v-model="dataForm.refundPrice" placeholder="退款金额"></el-input>
    </el-form-item>
    <el-form-item label="支付生成的订单号" prop="transactionId">
      <el-input v-model="dataForm.transactionId" placeholder="支付生成的订单号"></el-input>
    </el-form-item>
    <el-form-item label="订单支付编号" prop="outTradeNo">
      <el-input v-model="dataForm.outTradeNo" placeholder="订单支付编号"></el-input>
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
          nickname: '',
          orderId: '',
          price: '',
          givePrice: '',
          rechargeType: '',
          paid: '',
          payTime: '',
          addTime: '',
          refundPrice: '',
          transactionId: '',
          outTradeNo: ''
        },
        dataRule: {
          uid: [
            { required: true, message: '充值用户UID不能为空', trigger: 'blur' }
          ],
          nickname: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          orderId: [
            { required: true, message: '订单号不能为空', trigger: 'blur' }
          ],
          price: [
            { required: true, message: '充值金额不能为空', trigger: 'blur' }
          ],
          givePrice: [
            { required: true, message: '购买赠送金额不能为空', trigger: 'blur' }
          ],
          rechargeType: [
            { required: true, message: '充值类型不能为空', trigger: 'blur' }
          ],
          paid: [
            { required: true, message: '是否充值不能为空', trigger: 'blur' }
          ],
          payTime: [
            { required: true, message: '充值支付时间不能为空', trigger: 'blur' }
          ],
          addTime: [
            { required: true, message: '充值时间不能为空', trigger: 'blur' }
          ],
          refundPrice: [
            { required: true, message: '退款金额不能为空', trigger: 'blur' }
          ],
          transactionId: [
            { required: true, message: '支付生成的订单号不能为空', trigger: 'blur' }
          ],
          outTradeNo: [
            { required: true, message: '订单支付编号不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/admin/userrecharge/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.uid = data.userRecharge.uid
                this.dataForm.nickname = data.userRecharge.nickname
                this.dataForm.orderId = data.userRecharge.orderId
                this.dataForm.price = data.userRecharge.price
                this.dataForm.givePrice = data.userRecharge.givePrice
                this.dataForm.rechargeType = data.userRecharge.rechargeType
                this.dataForm.paid = data.userRecharge.paid
                this.dataForm.payTime = data.userRecharge.payTime
                this.dataForm.addTime = data.userRecharge.addTime
                this.dataForm.refundPrice = data.userRecharge.refundPrice
                this.dataForm.transactionId = data.userRecharge.transactionId
                this.dataForm.outTradeNo = data.userRecharge.outTradeNo
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
              url: this.$http.adornUrl(`/admin/userrecharge/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'uid': this.dataForm.uid,
                'nickname': this.dataForm.nickname,
                'orderId': this.dataForm.orderId,
                'price': this.dataForm.price,
                'givePrice': this.dataForm.givePrice,
                'rechargeType': this.dataForm.rechargeType,
                'paid': this.dataForm.paid,
                'payTime': this.dataForm.payTime,
                'addTime': this.dataForm.addTime,
                'refundPrice': this.dataForm.refundPrice,
                'transactionId': this.dataForm.transactionId,
                'outTradeNo': this.dataForm.outTradeNo
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
