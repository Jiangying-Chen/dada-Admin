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
          placeholder="用户ID搜索"
          clearable
        ></el-input>
      </el-form-item>
      <el-form-item>
        <!-- 选择框 -->
        <el-select v-model="dataForm.type" clearable placeholder="请选择类型">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          >
          </el-option>
        </el-select>

        <el-button @click="getDataList()">查询</el-button>
        <el-button @click="refresh()">重置</el-button>
        <el-button
          v-if="isAuth('admin:post:delete')"
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
        prop="id"
        header-align="center"
        align="center"
        label="ID"
      >
      </el-table-column>
      <el-table-column
        prop="userId"
        header-align="center"
        align="center"
        label="用户ID"
      >
      </el-table-column>
      <el-table-column
        prop="user.username"
        header-align="center"
        align="center"
        label="用户名"
      >
      </el-table-column>
      <el-table-column
        prop="user.avatar"
        header-align="center"
        align="center"
        width="100"
        label="用户头像"
      >
        <template slot-scope="scope">
          <img style="width: 40px; height: 40px" :src="scope.row.user.avatar" />
        </template>
      </el-table-column>
      <el-table-column
        prop="prizeId"
        header-align="center"
        align="center"
        label="奖品ID"
      >
      </el-table-column>
      <el-table-column
        prop="prizeType"
        header-align="center"
        align="center"
        label="奖品类型"
      >
        <template slot-scope="scope">
          <div>
            <el-tag v-if="scope.row.prizeType == 1" type="success">积分</el-tag>
            <el-tag v-else-if="scope.row.prizeType == 2" type="warning"
              >谢谢惠顾</el-tag
            >
            <el-tag v-else type="danger">商品</el-tag>  
            <!-- prizeType == 4 商品-->
          </div>
        </template>
      </el-table-column>
      <el-table-column
        prop="prizeName"
        header-align="center"
        align="center"
        label="奖品名称"
      >
      </el-table-column>

      <el-table-column
        prop="prizeImage"
        header-align="center"
        align="center"
        width="100"
        label="奖品图片"
      >
        <template slot-scope="scope">
          <img style="width: 40px; height: 40px" :src="scope.row.prizeImage" />
        </template>
      </el-table-column>
      <el-table-column
        prop="number"
        header-align="center"
        align="center"
        label="获得数量"
      >
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        width="160px"
        label="抽奖时间"
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
          <!-- <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button> -->
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
      layout="total, sizes, prev, pager, next, jumper"
    >
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update
      v-if="addOrUpdateVisible"
      ref="addOrUpdate"
      @refreshDataList="getDataList"
    ></add-or-update>
  </div>
</template>

<script>
import AddOrUpdate from "./luckdrawrecord-add-or-update";
export default {
  data() {
    return {
      dataForm: {
        key: "",
        type: "",
      },
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      addOrUpdateVisible: false,
      options: [
        {
          value: 1,
          label: "积分",
        },
        {
          value: 2,
          label: "谢谢惠顾",
        },
        {
          value: 3,
          label: "红包",
        },
      ],
    };
  },
  components: {
    AddOrUpdate,
  },
  activated() {
    this.getDataList();
  },
  methods: {
    refresh() {
      this.dataForm.key = "";
      this.dataForm.type = "";
      this.pageIndex = 1;
      this.pageSize = 10;
      this.getDataList();
    },
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/admin/luckdrawrecord/list"),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          key: this.dataForm.key,
          type: this.dataForm.type,
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
          url: this.$http.adornUrl("/admin/luckdrawrecord/delete"),
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
