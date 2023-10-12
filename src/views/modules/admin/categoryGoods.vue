<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
         <el-button type="primary" @click="addHandle()">新增</el-button>
      </el-form-item>
    </el-form>
    <el-table :data="dataList" border v-loading="dataListLoading" @selection-change="selectionChangeHandle"
      style="width: 100%;">
        <el-table-column label="分类名称" align="center" prop="name"/>
        <el-table-column label="排序" align="center" prop="sortOrder"/>
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
              <el-form-item label="分类名称" prop="name" >
                 <el-input v-model="form.name" autocomplete="off" placeholder="请填写分类名称"></el-input>
              </el-form-item>
          </el-col>
           <el-col :span="22">
              <el-form-item label="排序" prop="sortOrder" >
                 <el-input v-model.number="form.sortOrder" autocomplete="off" placeholder="请填写排序号"></el-input>
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
  import AddOrUpdate from './categoryGoods-add-or-update'
  export default {
    data() {
      return {
        form:{},
        dataForm: {
          key: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        userId:'',
        userName:'',
        title:"",
        rules:{
          name: [
            { required: true, message: "名称不能为空", trigger: "change" }
          ],
          sortOrder: [
            { required: true, message: "排序不能为空", trigger: "change" }
          ]
        }
      }
    },
    components: {
      AddOrUpdate
    },
    activated() {
      this.getDataList();
    },
    methods: {
    // 重置表单
    close(){
       this.form={}
        // 2.清空校验
      this.$refs.form.resetFields()

    },
      turnOnOff(row) {
        this.$confirm(`是否确认修改状态?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/admin/point/task/'+row.id+'/under?enable=' + row.enable),
            method: 'PUT',
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

      },
      addHandle(){
        this.addOrUpdateVisible = true;
        this.title = "添加";
      },
      // 获取数据列表
      getDataList() {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/admin/pointGoodsCategory'),
          method: 'get',
          params: this.$http.adornParams({
            'pageNumber': this.pageIndex,
            'pageSize': this.pageSize
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
      // 表单提交
      dataFormSubmit () {
        this.$refs['form'].validate((valid) => {
          if (valid) {
            console.log(this.form,'form===')
            this.$http({
              url: this.$http.adornUrl("/admin/pointGoodsCategory"),
              method: `${!this.form.id ? 'post' : 'PUT'}`,
              headers: {
                'Content-Type': 'application/x-www-form-urlencoded'
              },
              data: qs.stringify(this.form)

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
        this.addOrUpdateVisible = true;
        this.title = "添加";
      },
      // 删除
      deleteHandle(id) {
        this.$confirm(`确定对[id=${id}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/admin/pointGoodsCategory/'+ id),
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
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      },


    }
  }
</script>
