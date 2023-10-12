<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="帖子分类名称" prop="name">
      <el-input v-model="dataForm.name" placeholder="请输入帖子分类名称"></el-input>
    </el-form-item>
   <el-form-item label="选择圈子" prop="topicId">
    	<el-select v-model="dataForm.topicId" placeholder="请选择圈子" style="width:100%" filterable
    		clearable>
    		<el-option v-for='v in topicList' :key='v.id' :label="v.topicName" :value="v.id">
    		</el-option>
    	</el-select>
    </el-form-item>
  <!--  <el-form-item label="描述" prop="introduce">
      <el-input v-model="dataForm.introduce" placeholder="描述"></el-input>
    </el-form-item> -->
    <!-- <el-form-item label="浏览量" prop="readCount">
      <el-input v-model="dataForm.readCount" type="number" placeholder="浏览量"></el-input>
    </el-form-item> -->
    <el-form-item label="状态" prop="enable">
      <el-switch
        v-model="dataForm.enable">
      </el-switch>
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
           createTime: "",
          	enable: true,
          	id: 0,
          	name: "",
          	topicId: ''
        },
        topicList:[],
        dataRule: {

          topicId: [
            { required: true, message: '圈子id不能为空', trigger: 'blur' }
          ],
          name: [
            { required: true, message: '标题不能为空', trigger: 'blur' }
          ],
          enable: [
            { required: true, message: '状态不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/admin/postCategory/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                console.log(data,'=======')
                this.dataForm.topicId = data.result.topicId
                this.dataForm.name = data.result.name
                this.dataForm.enable = data.result.enable
                this.dataForm.createTime = data.result.createTime
              }
            })
          }else{
            this.dataForm={
           createTime: "",
          	enable: true,
          	id: 0,
          	name: "",
          	topicId: ''
        };
          }
        })
      },
      // 选择圈子列表
      getTopicList(){
        // this.dataListLoading = true;
        this.$http({
          url: this.$http.adornUrl("/admin/topic/list"),
          method: "get",
          // params: this.$http.adornParams({
          //   page: this.pageIndex,
          //   limit: this.pageSize,
          //   key: this.dataForm.key,
          // }),
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
            console.log(this.dataForm,'dataForm===')
            this.$http({
              url: this.$http.adornUrl(`/admin/postCategory/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'topicId': this.dataForm.topicId,
                'name': this.dataForm.name,
                'enable': this.dataForm.enable,
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
