<template>
  <div class="mod-config">
    <el-form
      :inline="true"
      :model="dataForm"
      @keyup.enter.native="getDataList()"
    >
      <el-form-item>
        <el-input
          v-model="dataForm.key"
          placeholder="请输入圈子名称或描述"
          clearable
        ></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button @click="refresh()">重置</el-button>
        <el-button
          v-if="isAuth('admin:topic:delete')"
          type="danger"
          @click="deleteHandle()"
          :disabled="dataListSelections.length <= 0"
          >批量删除</el-button
        >
      </el-form-item>
      <el-form-item>
         <el-button
            type="primary"
            @click="addOrUpdateHandle()"
            >新增</el-button
          >
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%"
    >
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50"
      >
      </el-table-column>
      <el-table-column
        prop="id"
        header-align="center"
        align="center"
        label="圈子id"
      >
      </el-table-column>
      <el-table-column
        prop="uid"
        header-align="center"
        align="center"
        label="用户id"
      >
      </el-table-column>
     <el-table-column
        prop="userInfo.avatar"
        header-align="center"
        align="center"
        label="头像"
      >
        <template slot-scope="scope" >
          <img
            style="width: 40px; height: 40px"
            :src="scope.row.userInfo.avatar"
          />
        </template>
      </el-table-column>
      <el-table-column
        prop="userInfo.username"
        header-align="center"
        align="center"
        label="用户名"
      >
      </el-table-column>
      <el-table-column
        prop="cateName"
        header-align="center"
        align="center"
        label="分类"
      >
      </el-table-column>
      <el-table-column
        prop="topicName"
        header-align="center"
        align="center"
        label="圈子名称"
      >
      </el-table-column>
      <el-table-column prop="description" align="center" label="描述" width="200">
        <template slot-scope="scope">
    	  {{scope.row.description.length > 15 ? scope.row.description.slice(0, 15) + "..." : scope.row.description}}
        <a class="post-content" v-if="scope.row.description.length > 15"
           @click="showDialog(scope.row)"
        >更多</a>
        </template>
      </el-table-column>
      <el-table-column
        prop="coverImage"
        header-align="center"
        align="center"
        width="100"
        label="圈子头像"
      >
        <template slot-scope="scope">
          <img style="width: 40px; height: 40px" :src="scope.row.coverImage" />
        </template>
      </el-table-column>
      <el-table-column
        prop="bgImage"
        header-align="center"
        align="center"
        width="100"
        label="背景图"
      >
        <template slot-scope="scope">
          <img style="width: 80px; height: 50px" :src="scope.row.bgImage" />
        </template>
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="圈子状态"
      >
        <template slot-scope="scope">
          <div>
            <el-tag v-if="scope.row.status == 0" type="success">正常</el-tag>
            <el-tag v-else type="danger">禁用</el-tag>
          </div>
        </template>
      </el-table-column>
      <el-table-column
        prop="userNum"
        header-align="center"
        align="center"
        label="加入人数"
      >
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        width="170"
        label="创建时间"
      >
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作"
      >
        <template slot-scope="scope">
          <el-button
            type="text"
            size="small"
            @click="addOrUpdateHandle(scope.row.id)"
            >修改</el-button
          >
          <el-button
            type="text"
            size="small"
            @click="deleteHandle(scope.row.id)"
            >删除</el-button
          >
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
    <add-or-update
      v-if="addOrUpdateVisible"
      ref="addOrUpdate"
      @refreshDataList="getDataList"
    ></add-or-update>
    <!-- 内容弹窗 -->
    <el-dialog :visible.sync="dialogVisible3" width="30%">
	    <span slot="title">
       	<i class="el-icon-edit"></i>
        <span>内容</span>
      </span>
    {{ postContent }}
    </el-dialog>
  </div>
</template>

<script>
import AddOrUpdate from "./topic-add-or-update";
export default {
  data() {
    return {
      dataForm: {
        key: "",
      },
      dialogVisible3: false,
      postContent:"",
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      addOrUpdateVisible: false,
    };
  },
  components: {
    AddOrUpdate,
  },
  activated() {
    this.getDataList();
  },
  methods: {
    showDialog(row) {
      this.dialogVisible3 = true;
      this.postContent = row.description;
    },
    refresh() {
      this.dataForm.key = "";
      this.pageIndex = 1;
      this.pageSize = 10;
      this.getDataList();
    },
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/admin/topic/list"),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          key: this.dataForm.key,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.totalPage = data.page.totalCount;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        console.log(this.dataList,'this.dataList=====')
        this.dataListLoading = false;
      });
    },
    // 每页数
    sizeChangeHandle(val) {
      this.pageSize = val;
      this.pageIndex = 1;
      this.getDataList();
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageIndex = val;
      this.getDataList();
    },
    // 多选
    selectionChangeHandle(val) {
      this.dataListSelections = val;
    },
    // 新增 / 修改
    addOrUpdateHandle(id) {
      this.addOrUpdateVisible = true;
      this.$nextTick(() => {
        this.$refs.addOrUpdate.init(id);
      });
    },
    // 删除
    deleteHandle(id) {
      var ids = id
        ? [id]
        : this.dataListSelections.map((item) => {
            return item.id;
          });
      this.$confirm(
        `确定对[id=${ids.join(",")}]进行[${id ? "删除" : "批量删除"}]操作?`,
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning",
        }
      ).then(() => {
        this.$http({
          url: this.$http.adornUrl("/admin/topic/delete"),
          method: "post",
          data: this.$http.adornData(ids, false),
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.$message({
              message: "操作成功",
              type: "success",
              duration: 1500,
              onClose: () => {
                this.getDataList();
              },
            });
          } else {
            this.$message.error(data.msg);
          }
        });
      });
    },
  },
};
</script>
<style lang="scss" scoped>
.post-content{
  font-size: 10px;
  color: rgb(58, 91, 222);
}
</style>
