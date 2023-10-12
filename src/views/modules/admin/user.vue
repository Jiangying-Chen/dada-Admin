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
          placeholder="用户ID/用户名/手机号/IP"
          clearable
        ></el-input>
      </el-form-item>
      <!-- 选择框 -->
      <el-select v-model="dataForm.type" clearable placeholder="请选择用户类型">
        <el-option
          v-for="item in options"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        >
        </el-option>
      </el-select>
      <el-select
        v-model="dataForm.status"
        clearable
        placeholder="请选择用户状态"
      >
        <el-option
          v-for="item in options2"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        >
        </el-option>
      </el-select>
      <el-select
        v-model="dataForm.vipStatus"
        clearable
        placeholder="请选择会员状态"
      >
        <el-option
          v-for="item in options3"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        >
        </el-option>
      </el-select>
      <el-form-item>
        <el-button @click="getDataList()" type="primary">查询</el-button>
        <el-button @click="refresh()">重置</el-button>
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
        prop="uid"
        header-align="center"
        align="center"
        label="用户id"
      >
      </el-table-column>
      <el-table-column
        prop="username"
        header-align="center"
        align="center"
        label="用户名"
      >
      </el-table-column>
      <el-table-column
        prop="avatar"
        header-align="center"
        align="center"
        label="头像"
        width="100"
      >
        <template slot-scope="scope">
          <img style="width: 40px; height: 40px" :src="scope.row.avatar" />
        </template>
      </el-table-column>
      <el-table-column
        prop="gender"
        header-align="center"
        align="center"
        label="性别"
      >
        <template slot-scope="scope">
          <div>
            <el-tag v-if="scope.row.gender == 1" type="success">男</el-tag>
            <el-tag v-else-if="scope.row.gender == 0">保密</el-tag>
            <el-tag v-else type="danger">女</el-tag>
          </div>
        </template>
      </el-table-column>

      <el-table-column
        prop="mobile"
        header-align="center"
        align="center"
        label="手机号"
      >
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="状态"
      >
        <template slot-scope="scope">
          <div>
            <el-tag v-if="scope.row.status == 0" type="success">正常</el-tag>
            <el-tag v-else-if="scope.row.status == 1" type="warning"
              >禁用</el-tag
            >
          </div>
        </template>
      </el-table-column>
      <el-table-column
        prop="intro"
        header-align="center"
        align="center"
        label="个性签名"
      >
      </el-table-column>
      <el-table-column
        prop="integral"
        header-align="center"
        align="center"
        label="积分"
      >
      </el-table-column>
      <el-table-column
        prop="money"
        header-align="center"
        align="center"
        label="余额"
      >
      </el-table-column>

      <el-table-column
        prop="tagStr"
        header-align="center"
        align="center"
        label="标签"
      >
      </el-table-column>
      <el-table-column
        prop="vip"
        header-align="center"
        align="center"
        label="用户类型"
      >
        <template slot-scope="scope">
          <div>
            <el-tag v-if="scope.row.vip == 0" type="success">普通用户</el-tag>
            <el-tag v-else-if="scope.row.vip == 1">会员用户</el-tag>
          </div>
        </template>
      </el-table-column>
      <el-table-column
        prop="type"
        header-align="center"
        align="center"
        label="账号类型"
      >
        <template slot-scope="scope">
          <div>
            <el-tag v-if="scope.row.type == 1" type="success">官方账号</el-tag>
            <el-tag v-else-if="scope.row.type == 0">普通账号</el-tag>
            <el-tag v-else type="info">虚拟账号</el-tag>
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
        prop="updateTime"
        header-align="center"
        align="center"
        label="更新时间"
      >
      </el-table-column>
      <el-table-column
        prop="city"
        header-align="center"
        align="center"
        label="IP城市"
      >
      </el-table-column>
      <el-table-column
        prop="lastLoginIp"
        header-align="center"
        align="center"
        label="登录ip"
      >
      </el-table-column>
      <el-table-column
        prop="openid"
        header-align="center"
        align="center"
        width="150"
        label="小程序openid"
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
            @click="addOrUpdateHandle(scope.row.uid)"
            >更改</el-button
          >
          <el-button type="text" size="small" @click="punishOpen(scope.row.uid)"
            >处理</el-button
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
    <!-- 处理框 -->
    <el-dialog
      title="处罚管理"
      :visible.sync="dialogVisible"
      width="30%"
      :before-close="handleClose"
    >
      <el-form>
        <el-form-item label="帖子" prop="resetPost">
          <el-radio-group v-model="punish.resetPost">
            <el-radio :label="2">全部删除</el-radio>
            <el-radio :label="1">全部下架</el-radio>
            <el-radio :label="0">不处理</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="头像" prop="resetAvatar">
          <el-radio-group v-model="punish.resetAvatar">
            <el-radio :label="1">重置</el-radio>
            <el-radio :label="0">不重置</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="昵称" prop="resetUsername">
          <el-radio-group v-model="punish.resetUsername">
            <el-radio :label="1">重置</el-radio>
            <el-radio :label="0">不重置</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="签名" prop="resetIntro">
          <el-radio-group v-model="punish.resetIntro">
            <el-radio :label="1">重置</el-radio>
            <el-radio :label="0">不重置</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="doPunish">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import AddOrUpdate from "./user-add-or-update";
export default {
  data() {
    return {
      dataForm: {
        key: "",
        status: "",
        type: "",
        vipStatus: "",
      },
      punish: {
        resetPost: 0,
        resetAvatar: 0,
        resetIntro: 0,
        resetUsername: 0,
        uid: 0,
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
          value: 0,
          label: "普通用户",
        },
        {
          value: 1,
          label: "官方账户",
        },
        {
          value: 2,
          label: "虚拟用户",
        },
      ],
      options2: [
        {
          value: 0,
          label: "正常",
        },
        {
          value: 1,
          label: "禁用",
        },
      ],
      options3: [
        {
          value: 0,
          label: "普通用户",
        },
        {
          value: 1,
          label: "会员用户",
        },
      ],
      dialogVisible: false,
    };
  },
  components: {
    AddOrUpdate,
  },
  activated() {
    this.getDataList();
  },
  methods: {
    handleClose(){},
    punishOpen(uid) {
      this.punish.uid = uid;
      this.dialogVisible = true;
    },
    refresh() {
      this.dataForm.key = "";
      this.dataForm.type = "";
      this.dataForm.status = "";
      this.dataForm.vipStatus = "";
      this.pageIndex = 1;
      this.pageSize = 10;
      this.getDataList();
    },
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/admin/user/list"),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          key: this.dataForm.key,
          type: this.dataForm.type,
          status: this.dataForm.status,
          vipStatus: this.dataForm.vipStatus,
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
            return item.uid;
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
          url: this.$http.adornUrl("/admin/user/delete"),
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
    //对账户处罚
    doPunish() {
      this.dialogVisible = false;
      this.$http({
        url: this.$http.adornUrl(`/admin/user/punish`),
        method: "post",
        data: this.punish,
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
    },
    // 禁用
    banHandle(id) {
      this.$confirm(`确定对该用户禁用操作?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      }).then(() => {
        this.$http({
          url: this.$http.adornUrl(`/admin/user/ban/${id}`),
          method: "get",
          params: this.$http.adornParams(),
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

    // 解除禁用
    openBanHandle(id) {
      this.$confirm(`确定对该用户解除禁用操作?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      }).then(() => {
        this.$http({
          url: this.$http.adornUrl(`/admin/user/openBan/${id}`),
          method: "get",
          params: this.$http.adornParams(),
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
