<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="标题" prop="title">
      <el-input v-model="dataForm.title" placeholder="标题"></el-input>
    </el-form-item>
    <el-form-item label="图片" prop="img">
        <el-upload
          class="avatar-uploader"
          :action="url"
          :show-file-list="false"
          :on-success="handleIconSuccess"
        >
          <img v-if="dataForm.img" :src="dataForm.img" class="avatar" />
          <i v-else class="el-icon-plus avatar-uploader-icon" />
        </el-upload>
        <p class="formInfo">建议尺寸：200*200像素，jpg、png图片类型</p>
      </el-form-item>
    <el-form-item label="跳转路径" prop="url">
      <el-input v-model="dataForm.url" placeholder="跳转路径"></el-input>
    </el-form-item>
    <el-form-item label="跳转类型" prop="type">
        <el-radio-group v-model="dataForm.type">
          <el-radio :label="0">页面</el-radio>
          <el-radio :label="1">外链</el-radio>
        </el-radio-group>
        <p class="formInfo">外链就是外部网站的链接，页面指项目内部页面的跳转</p>
    </el-form-item>
        <el-form-item label="状态" prop="status">
        <el-radio-group v-model="dataForm.status">
          <el-radio :label="0">正常</el-radio>
          <el-radio :label="1">禁用</el-radio>
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
          title: '',
          img: '',
          url: '',
          type: 0,
          status: 0,
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          title: [
            { required: true, message: '标题不能为空', trigger: 'blur' }
          ],
          img: [
            { required: true, message: '图片不能为空', trigger: 'blur' }
          ],
          url: [
            { required: true, message: '跳转路径不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '跳转类型0页面1外链不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '状态0正常1禁用不能为空', trigger: 'blur' }
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
        this.url = this.$http.adornUrl(
        `/sys/oss/upload?token=${this.$cookie.get("token")}`
      );
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/admin/navigation/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.title = data.navigation.title
                this.dataForm.img = data.navigation.img
                this.dataForm.url = data.navigation.url
                this.dataForm.type = data.navigation.type
                this.dataForm.status = data.navigation.status
                this.dataForm.createTime = data.navigation.createTime
                this.dataForm.updateTime = data.navigation.updateTime
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
              url: this.$http.adornUrl(`/admin/navigation/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'title': this.dataForm.title,
                'img': this.dataForm.img,
                'url': this.dataForm.url,
                'type': this.dataForm.type,
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
      },
      handleIconSuccess(response) {
        this.dataForm.img = response.url;
        this.$forceUpdate();
      },
    }
  }
</script>
<style lang="scss">
.formInfo {
  line-height: 0px;
  color: #999999;
}
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409eff;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 100px;
  height: 100px;
  line-height: 100px;
  text-align: center;
}
.avatar {
  width: 100px;
  height: 100px;
  display: block;
}
</style>