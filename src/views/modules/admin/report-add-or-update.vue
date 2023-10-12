<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    
    <el-form-item label="描述" prop="content">
      <el-input v-model="dataForm.content" placeholder="描述" type="textarea" disabled></el-input>
    </el-form-item>
    <el-form-item label="状态" prop="status">
        <el-radio-group v-model="dataForm.status">
          <el-radio :label="2">驳回</el-radio>
          <el-radio :label="1">受理</el-radio>
        </el-radio-group>
    </el-form-item>
    <el-form-item label="平台反馈" prop="feedback">
      <el-input v-model="dataForm.feedback" placeholder="反馈信息将发送给举报用户"  type="textarea"></el-input>
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
          media: '',
          content: '',
          uid: '',
          type: '',
          status: '',
          feedback: '',
          linkId: '',
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          media: [
            { required: true, message: '文件不能为空', trigger: 'blur' }
          ],
          content: [
            { required: true, message: '描述不能为空', trigger: 'blur' }
          ],
          uid: [
            { required: true, message: '用户id不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '类型1帖子 2评论 3用户 4圈子不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '状态0待审核 1已处理 2已驳回不能为空', trigger: 'blur' }
          ],
          feedback: [
            { required: true, message: '平台反馈不能为空', trigger: 'blur' }
          ],
          linkId: [
            { required: true, message: '关联id不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          updateTime: [
            { required: true, message: '更新时间不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/admin/report/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.media = data.report.media
                this.dataForm.content = data.report.content
                this.dataForm.uid = data.report.uid
                this.dataForm.type = data.report.type
                this.dataForm.status = data.report.status
                this.dataForm.feedback = data.report.feedback
                this.dataForm.linkId = data.report.linkId
                this.dataForm.createTime = data.report.createTime
                this.dataForm.updateTime = data.report.updateTime
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
              url: this.$http.adornUrl(`/admin/report/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'media': this.dataForm.media,
                'content': this.dataForm.content,
                'uid': this.dataForm.uid,
                'type': this.dataForm.type,
                'status': this.dataForm.status,
                'feedback': this.dataForm.feedback,
                'linkId': this.dataForm.linkId,
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
