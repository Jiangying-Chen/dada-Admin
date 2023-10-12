<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="标题" prop="title">
      <el-input v-model="dataForm.title" placeholder="标题"></el-input>
    </el-form-item>
   <el-form-item label="选择圈子" prop="topicId">
   	<el-select v-model="dataForm.topicId" placeholder="请选择圈子" style="width:100%" filterable
   		clearable>
   		<el-option v-for='v in topicList' :key='v.id' :label="v.topicName" :value="v.id">
   		</el-option>
   	</el-select>
   </el-form-item>
    <el-form-item label="描述" prop="introduce">
      <el-input v-model="dataForm.introduce" placeholder="描述"></el-input>
    </el-form-item>
    <el-form-item label="浏览量" prop="readCount">
      <el-input v-model="dataForm.readCount" type="number" placeholder="浏览量"></el-input>
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
          topicId: '',
          title: '',
          introduce: '',
          readCount: '',
          topType: '',
          createTime: ''
        },
        topicList:[],
        dataRule: {
          uid: [
            { required: true, message: '用户id不能为空', trigger: 'blur' }
          ],
          topicId: [
            { required: true, message: '圈子id不能为空', trigger: 'blur' }
          ],
          title: [
            { required: true, message: '标题不能为空', trigger: 'blur' }
          ],
          introduce: [
            { required: true, message: '描述不能为空', trigger: 'blur' }
          ],
          readCount: [
            { required: true, message: '浏览量不能为空', trigger: 'blur' }
          ],
          topType: [
            { required: true, message: '推荐位置：0 不推荐  1 首页推荐不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    created() {
      this.getTopicList();
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/admin/discuss/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.uid = data.discuss.uid
                this.dataForm.topicId = data.discuss.topicId
                this.dataForm.title = data.discuss.title
                this.dataForm.introduce = data.discuss.introduce
                this.dataForm.readCount = data.discuss.readCount
                this.dataForm.topType = data.discuss.topType
                this.dataForm.createTime = data.discuss.createTime
              }
            })
          }
        })
      },
      // 选择圈子列表
      getTopicList(){
        // this.dataListLoading = true;
        this.$http({
          url: this.$http.adornUrl("/admin/topic/list"),
          method: "get",
          params: this.$http.adornParams({
            page: 1,
            limit: 10000,
            // key: this.dataForm.key,
          }),
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.topicList = data.page.list;
          } else {
            this.topicList = [];
          }
          // this.dataListLoading = false;
        });
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/admin/discuss/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'uid': this.topicList.filter(v => v.id ==this.dataForm.topicId)[0].uid,
                'topicId': this.dataForm.topicId,
                'title': this.dataForm.title,
                'introduce': this.dataForm.introduce,
                'readCount': this.dataForm.readCount,
                'topType': this.dataForm.topType,
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
