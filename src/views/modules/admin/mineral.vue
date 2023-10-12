<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-row>
        <el-col :span="17">
          <el-form-item >
             <el-button  type="primary" @click="addHandle()">新增</el-button>
          </el-form-item>
        </el-col>
        <el-col :span="7" style="text-align: right;">
          <el-form-item>
             <div class="top-right-btn" style=" font-size: 14px;color: red;">矿石每30分钟刷新一颗</div>
           </el-form-item>
        </el-col>
      </el-row>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column label="名称" align="center" prop="name">
      </el-table-column>
      <el-table-column label="兑换积分" align="center" prop="changePoint"/>
       <el-table-column label="图片"  align="center" prop="image">
       <template slot-scope="scope">
              <img :src="scope.row.image" style="width: 100px;height: 100px;"/>
          </template>
       </el-table-column>
      <el-table-column label="刷新概率" align="center" prop="refresh"/>
      <el-table-column label="状态" align="center" prop="status">
        <template slot-scope="scope">
          <view v-if="scope.row.status==1" >开启</view>
          <el-tag style="cursor: pointer" v-if="scope.row.status=='1'">开启</el-tag>
          <el-tag  v-if="scope.row.status=='0'" type="info">关闭</el-tag>

        </template>
      </el-table-column>
      <el-table-column fixed="right" header-align="center" align="center" width="150" label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination @size-change="sizeChangeHandle" @current-change="currentChangeHandle" :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]" :page-size="pageSize" :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <el-dialog :title="title" :visible.sync="addOrUpdateVisible" width="780px" append-to-body @close="close">
      <el-form ref="form" :model="form" :rules="rules" label-width="130px">
        <el-row>
          <el-col :span="24">
            <el-form-item label="id" style="display: none">
              <el-input v-model="form.id" autocomplete="off"></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="22">
              <el-form-item label="矿石名称" prop="name" >
                  <el-input v-model="form.name" autocomplete="off" placeholder="请输入矿石名称"></el-input>
              </el-form-item>
          </el-col>
           <el-col :span="22">
              <el-form-item label="兑换积分" prop="changePoint" >
                  <el-input v-model="form.changePoint" autocomplete="off" placeholder="请输入兑换积分"></el-input>
              </el-form-item>
          </el-col>
          <el-col :span="22">
              <el-form-item label="刷新概率" prop="refresh" >
                  <el-input v-model="form.refresh" autocomplete="off" placeholder="请输入刷新概率">
                    <template slot="append">%</template>
                  </el-input>
                  <el-col style="color: red;">注：所有矿石的刷新概率总计为100%</el-col>
              </el-form-item>
          </el-col>
          <el-col :span="22">
            <el-form-item label="图标" prop="image" required>
              <el-upload
                class="avatar-uploader"
                :action="url"
                :show-file-list="false"
                :on-success="handleIconSuccess"
              >
                <img v-if="form.image" :src="form.image" class="avatar" />
                <i v-else class="el-icon-plus avatar-uploader-icon" />
              </el-upload>
            </el-form-item>
          </el-col>
          <el-col :span="22">
              <el-form-item label="状态" prop="status" >
                <el-switch v-model="form.status"  active-value="1" inactive-value="0"></el-switch>
              </el-form-item>
          </el-col>
        </el-row>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="dataFormSubmit">确 定</el-button>
        <el-button @click="addOrUpdateVisible=false">取 消</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
  import qs from 'qs'
  import AddOrUpdate from './product-add-or-update'
  export default {
    data() {
      return {
        myfileList:[],
        slideshowList:[],
        slideList:[],
        dataForm: {
          key: ''
        },
        title:"",
        rules:{
          name: [
            { required: true, message: "名称不能为空", trigger: "change" }
          ],
          changePoint: [
            { required: true, message: "兑换积分不能为空", trigger: "change" }
          ],
          refresh:[
            { required: true, message: "刷新概率不能为空", trigger: "change" }
          ],

        },
        form:{},
        url:'',
        typeList:[],
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        userId:'',
        userName:''
      }
    },
    components: {
      AddOrUpdate
    },
    created() {
      this.url=this.$http.adornUrl(
        `/sys/oss/upload?token=${this.$cookie.get("token")}`
      );
    },
    activated() {
      this.getDataList();
    },
    methods: {

      handleIconSuccess(response) {
        this.form.image = response.url;
        this.$forceUpdate();
      },
      // 重置表单
      close(){
         this.form={}
          // 2.清空校验
        this.$refs.form.resetFields()

      },
      // 获取数据列表
      getDataList() {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/app/mineral/getByPage'),
          method: 'get',
          params: this.$http.adornParams({
            'pageNumber': this.pageIndex,
            'pageSize': this.pageSize,
          })
        }).then(({
          data
        }) => {
          if (data && data.code === 0) {
            this.dataList = data.result.records
            this.totalPage = data.result.total
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 每页数
      sizeChangeHandle(val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle(val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle(val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle(row) {
        this.addOrUpdateVisible = true;
        this.title = "修改";
        this.form={...row};
      },
      addHandle(){
        this.title = "添加";
        this.addOrUpdateVisible = true;
        this.form={}
        // this.$nextTick(() => {
        //         this.$refs['form'].resetFields();
        //         this.resetTemp()

        //     })

        // this.$refs['form'].resetFields();
        // this.addOrUpdateVisible = true;

      },
      // resetForm(form) {
      //     if (this.$refs['form']!==undefined) {
      //         // 清除表单内容和清除表单验证消息
      //         this.$refs['form'].resetFields();
      //     }
      // },

      dataFormSubmit () {
        this.$refs['form'].validate((valid) => {
          if (valid) {
            console.log(this.form,'form===')
            this.$http({
              url: this.$http.adornUrl(`/app/mineral/${!this.form.id ? 'save' : 'update'}`),
              method: `${!this.form.id ? 'post' : 'PUT'}`,
              // headers: {
              //   'Content-Type': 'application/x-www-form-urlencoded'
              // },qs.stringify(this.form)
              data: this.form

            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.addOrUpdateVisible = false;

                    this.form={};
                    this.getDataList()
                    // this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      },
      // 删除
      deleteHandle(id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/app/mineral/delete/'+ id),
            method: 'DELETE',
          }).then(({
            data
          }) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList();

                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      },
      //清理话题下的所有帖子
      deleteForce(id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]下的所有帖子分类进行[${id ? '删除' : '批量删除'}]操作(话题不删除)?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/admin/pointGoods/deletePostInDiscuss'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({
            data
          }) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
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


