<template>
  <div class="mod-config">
    <el-form
      :inline="true"
      :model="dataForm"
      @keyup.enter.native="getDataList()"
    >
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="推送标题 搜索" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <!-- 选择框 -->
        <el-select v-model="dataForm.type" clearable placeholder="请选择类型">
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
          v-if="isAuth('admin:message:delete')"
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
        prop="mid"
        header-align="center"
        align="center"
        label="消息id"
      >
      </el-table-column>
      <el-table-column
        prop="fromUid"
        header-align="center"
        align="center"
        label="发送者uid"
      >
      </el-table-column>
      <el-table-column
        prop="toUid"
        header-align="center"
        align="center"
        label="接收者uid"
      >
      </el-table-column>
      <el-table-column
        prop="postId"
        header-align="center"
        align="center"
        label="帖子id"
      >
      </el-table-column>
      <el-table-column
        prop="title"
        header-align="center"
        align="center"
        label="推送标题"
      >
      </el-table-column>

      <el-table-column prop="content" align="center" label="消息内容" width="200">
        <template slot-scope="scope">
    	  {{scope.row.content.length > 25 ? scope.row.content.slice(0, 25) + "..." : scope.row.content}}
        <a class="post-content" v-if="scope.row.content.length > 25"
           @click="showDialog(scope.row)"
        >更多</a>
        </template>
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="是否已读"
      >
        <template slot-scope="scope">
          <div>
            <el-tag v-if="scope.row.status == 0" type="success">未读</el-tag>
            <el-tag v-else-if="scope.row.status == 1" type="danger"
              >已读</el-tag
            >
          </div>
        </template>
      </el-table-column>
      <el-table-column
        prop="type"
        header-align="center"
        align="center"
        label="消息类型"
      >
        <template slot-scope="scope">
          <div>
            <el-tag v-if="scope.row.type == 1" type="success">点赞</el-tag>
            <el-tag v-else-if="scope.row.type == 2" type="warning">评论</el-tag>
            <el-tag v-else-if="scope.row.type == 3" type="info">收藏</el-tag>
            <el-tag v-else-if="scope.row.type == 4" >关注</el-tag>
            <el-tag v-else-if="scope.row.type == 5" type="danger"
              >系统通知</el-tag
            >
            <el-tag v-else type="danger">私聊</el-tag>
          </div>
        </template>
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
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
          <!-- <el-button
            type="text"
            size="small"
            @click="addOrUpdateHandle(scope.row.mid)"
            >修改</el-button
          > -->
          <el-button
            type="text"
            size="small"
            @click="deleteHandle(scope.row.mid)"
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
      layout="total, sizes, prev, pager, next, jumper"
    >
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update
      v-if="addOrUpdateVisible"
      ref="addOrUpdate"
      @refreshDataList="getDataList"
    ></add-or-update>
    <!-- 消息内容弹窗 -->
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
import AddOrUpdate from "./message-add-or-update";
export default {
  data() {
    return {
      dataForm: {
        key: "",
        type: ''
      },
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      addOrUpdateVisible: false,
      options: [{
          value: 3,
          label: '点赞收藏'
        }, {
          value: 2,
          label: '评论'
        }, {
          value: 4,
          label: '关注'
        }, {
          value: 5,
          label: '系统通知'
        }],
      dialogVisible3: false,
      postContent:"",
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
      this.postContent = row.content;
    },
    refresh(){
        this.dataForm.key='';
        this.dataForm.type='';
        this.pageIndex=1;
        this.pageSize=10;
        this.getDataList();
    },
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/admin/message/list"),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          key: this.dataForm.key,
          type: this.dataForm.type
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.totalPage = data.page.totalCount;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
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
            return item.mid;
          });
      this.$confirm(
        `确定对[id=${ids.join(",")}]进行[${id ? "删除" : "批量删除"}]操作`,
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning",
        }
      ).then(() => {
        this.$http({
          url: this.$http.adornUrl("/admin/message/delete"),
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