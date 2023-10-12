<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="150px">
    <el-form-item label="积分分类名称" prop="pointTaskName">
      <el-input v-model="dataForm.pointTaskName" placeholder="请输入积分任务名称"></el-input>
    </el-form-item>
   <el-form-item label="积分值" prop="pointValue">
    <el-input v-model="dataForm.pointValue" placeholder="请输入积分值"></el-input>
    </el-form-item>

    <el-form-item label="类型" prop="pointTaskType">
      <el-select v-model="dataForm.pointTaskType" clearable placeholder="请选择类型">
        <el-option key="0" value="beginner" label="新手"/>
        <el-option key="1" value="special" label="特殊" />
        <el-option key="2" value="daily" label="日常" />
      </el-select>
    </el-form-item>
   
    
    <el-form-item label="任务类型" prop="type">
      <el-select v-model="dataForm.type" clearable placeholder="任务类型">
        <el-option
          v-for="dict in typeList"
          :key="dict.label"
          :label="dict.label"
          :value="dict.label"
        />
       <!-- <el-option key="0" value="online" label="在线任务"/>
        <el-option key="1" value="pointOrder" label="兑换任务" />
        <el-option key="2" value="daily" label="日常" /> -->
      </el-select>
      <!-- <el-input v-model="dataForm.type" placeholder="任务类型"></el-input> -->
    </el-form-item>
    <el-form-item label="任务所需值" prop="taskNumber">
     <el-input v-model="dataForm.taskNumber" placeholder="请输入任务值">
        <template slot="append" v-if="dataForm.type=='在线任务'">分钟</template>
     </el-input>
     </el-form-item>
    <el-form-item label="任务图标" prop="img">
      <el-upload
        class="avatar-uploader"
        :action="url"
        :show-file-list="false"
        :on-success="handleIconSuccess"
      >
        <img v-if="dataForm.img" :src="dataForm.img" class="avatar" />
        <i v-else class="el-icon-plus avatar-uploader-icon" />
      </el-upload>
      <!-- <p class="formInfo">建议尺寸：500*250像素，jpg、png图片类型</p> -->
    </el-form-item>

    <el-form-item label="跳转路径" prop="skipUrl">
       <el-input v-model="dataForm.skipUrl" placeholder="请输入跳转路径"></el-input>
     </el-form-item>
     <el-form-item label="描述" prop="introduce">
        <el-input v-model="dataForm.introduce" placeholder="描述"></el-input>
      </el-form-item>
    <el-form-item label="状态" prop="enable">
      <el-switch
        v-model="dataForm.enable" active-value="START" inactive-value="END">
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
  import { filter } from 'lodash';
import qs from 'qs'
  export default {
    data () {
      return {
        url:'',
        visible: false,
        dataForm: {
           createTime: "",
          	enable: "END",
          	id: 0,
            pointTaskName:"",
            taskNumber:"",
            pointValue:"",
            introduce:'',
            pointTaskType:"",
            type:"",
            img:"",
        },
        typeList:[
          {label:'评论任务',value:'comment'},
          {label:'签到任务',value:'sign_in'},
          {label:'兑换任务',value:'pointOrder'},
          {label:'在线任务',value:'online'},
          {label:'邀新任务',value:'invite_new'},
           {label:'挖矿任务',value:'mining'},
          {label:'完善个人资料',value:'modification'},
          {label:'飞往其他星球',value:'change_topic'},
          {label:'特殊任务(邀请N个新人)',value:'invite_new_group'},
          {label:'发帖任务',value:'post_message'},
          {label:'实名认证任务',value:'authentication'},
          {label:'黑洞助手问答',value:'black_hole_qa'},
        ],
        topicList:[],
        dataRule: {

          pointTaskName: [
            { required: true, message: '积分任务名称不能为空', trigger: 'blur' }
          ],
          taskNumber: [
            { required: true, message: '任务所需值不能为空', trigger: 'blur' }
          ],
          enable: [
            { required: true, message: '状态不能为空', trigger: 'blur' }
          ],
          pointValue: [
            { required: true, message: '任务值不能为空', trigger: 'blur' }
          ],
          pointTaskType: [
            { required: true, message: '任务类型不能为空', trigger: 'blur' }
          ],
          // skipUrl: [
          //   { required: true, message: '介绍不能为空', trigger: 'blur' }
          // ],
          type: [
            { required: true, message: '类型不能为空', trigger: 'blur' }
          ],

        }
      }
    },
   created() {
      this.getTopicList();
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
              url: this.$http.adornUrl(`/admin/point/task/getInfo/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm=data.result;
                this.dataForm.type=this.typeList.filter(v=>v.value==data.result.type)[0].label
                console.log(data,'=======')
                // this.dataForm.topicId = data.result.topicId
                // this.dataForm.name = data.result.name
                // this.dataForm.enable = data.result.enable
                // this.dataForm.createTime = data.result.createTime
              }
            })
          }else{
            this.dataForm={
                // createTime: "",
                enable: "END",
                 // id: 0,
                 pointTaskName:"",
                 taskNumber:"",
                 pointValue:"",
                 introduce:'',
                 pointTaskType:"",
                 type:"",
                 img:"",
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
      handleIconSuccess(response) {
        this.dataForm.img = response.url;
        this.$forceUpdate();
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.dataForm.type=this.typeList.filter(v=>v.label==this.dataForm.type)[0].value
            console.log(this.dataForm,'dataForm===')
            // this.dataForm.type=this.typeList.filter(v=>v.value==data.result.type)[0].label
            this.$http({
              url: this.$http.adornUrl(`/admin/point/task/${!this.dataForm.id ? 'save' : 'edit'}`),
              method: `${!this.dataForm.id ? 'post' : 'PUT'}`,
              headers: {
                'Content-Type': 'application/x-www-form-urlencoded'
              },
              data: qs.stringify(this.dataForm)

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
  width:100px;
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
