<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="用户ID或描述 搜索" clearable></el-input>
      </el-form-item>

      <el-form-item>
      <!-- 选择框 -->
      <el-select v-model="dataForm.type" clearable placeholder="请选择状态">
        <el-option
          v-for="item in options"
          :key="item.value"
          :label="item.label"
          :value="item.value">
        </el-option>
     </el-select>

      <el-button @click="getDataList()">查询</el-button>
      <el-button @click="refresh()">重置</el-button>
      <el-button
          v-if="isAuth('admin:report:delete')"
          type="danger"
          @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0"
          >批量删除</el-button
        >

      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column
        prop="id"
        header-align="center"
        align="center"
        label="ID">
      </el-table-column>
      <el-table-column
        prop="uid"
        header-align="center"
        align="center"
        label="用户id">
      </el-table-column>
            <el-table-column
        prop="userInfo.username"
        header-align="center"
        align="center"
        label="用户名"
      >
      </el-table-column>
      <el-table-column
        prop="userInfo.avatar"
        header-align="center"
        align="center"
        label="头像"
      >
        <template slot-scope="scope">
          <img
            style="width: 40px; height: 40px"
            :src="scope.row.userInfo.avatar"
          />
        </template>
      </el-table-column>
      <el-table-column
        prop="type"
        header-align="center"
        align="center"
        label="类型">
        <template slot-scope="scope">
          <div>
            <el-tag v-if="scope.row.type == 1" type="success">帖子</el-tag>
            <el-tag v-else-if="scope.row.type == 2">评论</el-tag>
            <el-tag v-else-if="scope.row.type == 3" type="warning">用户</el-tag>
            <el-tag v-else type="danger">圈子</el-tag>
          </div>
        </template>
      </el-table-column>
      <el-table-column
        prop="media"
        header-align="center"
        align="center"
        label="文件">
        <template slot-scope="scope">
          <el-button type="text" @click="openPic(scope.row.media)">点击查看</el-button>
        </template>
      </el-table-column>
      <el-table-column
        prop="content"
        header-align="center"
        align="center"
        label="描述">
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="状态">
        <template slot-scope="scope">
          <div>
            <el-tag v-if="scope.row.status == 0" type="warning">待审核</el-tag>
            <el-tag v-else-if="scope.row.status == 1" type="success">已处理</el-tag>
            <el-tag v-else-if="scope.row.status == 2" type="danger">已驳回</el-tag>
          </div>
        </template>
      </el-table-column>
      <el-table-column
        prop="feedback"
        header-align="center"
        align="center"
        label="平台反馈">
      </el-table-column>
      <!-- <el-table-column
        prop="linkId"
        header-align="center"
        align="center"
        label="关联id">
      </el-table-column> -->
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        label="创建时间">
      </el-table-column>
      <el-table-column
        prop="updateTime"
        header-align="center"
        align="center"
        label="更新时间">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" v-if="scope.row.status == 0" @click="addOrUpdateHandle(scope.row.id)">处理</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
    <!-- 预览弹窗 -->
        <el-dialog
      title="图片预览"
      :visible.sync="dialogVisible2"
      width="60%"
      :before-close="handleClose"
    >
    <div class="images">
      <div v-for="(item, index) in media" :key="index" class="image-middle">  
        <el-card shadow="hover" :body-style="{ padding: '10px' }" >     
        <img :src="media[index]" class="image" @click="goPic(media[index])"/> 
        <div style="text-align:center;padding-top:12px">
        <span>图{{index+1}}</span>
        </div>
        </el-card>
      </div>
    </div>

      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible2 = false">取 消</el-button>
        <el-button type="primary" @click="dialogVisible2 = false"
          >确 定</el-button
        >
      </span>
    </el-dialog>
  </div>
</template>

<script>
  import AddOrUpdate from './report-add-or-update'
  export default {
    data () {
      return {
        dataForm: {
          key: '',
          type: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        dialogVisible2: false,
        media: [],
        options: [{
          value: 0,
          label: '待审核'
        }, {
          value: 1,
          label: '已处理'
        }, {
          value: 2,
          label: '已驳回'
        }],
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
      refresh(){
        this.dataForm.key='';
        this.dataForm.type='';
        this.pageIndex=1;
        this.pageSize=10;
        this.getDataList();
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/admin/report/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'key': this.dataForm.key,
            'type':this.dataForm.type
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      openPic(media){
        this.dialogVisible2=true;
        this.media=media;
      },
      goPic(url){
      window.open(url);
     },
      handleClose(done) {
        this.$confirm('确认关闭？')
          .then(_ => {
            done();
          })
          .catch(_ => {});
    },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/admin/report/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
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
<style lang="scss" scoped>
.video {
  width: 100%;
  margin-bottom: 10px;
}
.position {
  margin-left: 15px;
  font-size: 30px;
  font-weight: 600;
}
/* 图片总布局，样式 */
.images{
  display: flex;
  margin-top: 20px;
  margin-left: 21px;
  margin-right: 20px;
  flex-wrap: wrap;
}
/* 图片之间 */
.image-middle{
  margin-right: 15px;
  margin-bottom: 15px;
}
/* 单张图片样式 */
.image{
  width:110px;
  height: 110px;
}

</style>