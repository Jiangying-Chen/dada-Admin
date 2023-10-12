<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()" label-width="80px">
      <el-form-item label="状态">
        <el-select
          v-model="dataForm.status"
          placeholder="请选择用户状态"
          clearable
        >
        <el-option label="未支付" value="UNPAID"></el-option>
        <el-option label="待核销" value="TAKE"></el-option>
        <el-option label="已核销" value="CHECKED"></el-option>
        </el-select>
        </el-form-item>
        <el-form-item label="用户UID">
        <el-input
          v-model="dataForm.memberId"
          placeholder="请填写用户UID"
          clearable
        >
        </el-input>
        </el-form-item>
        <el-form-item  label="手机号">
        <el-input
          v-model="dataForm.phone"
          placeholder="请填写用户手机号"
          clearable
        >
        </el-input>
        </el-form-item>
      <el-form-item>
        
        
        <!-- 选择框1 -->
        <!-- <el-select v-model="dataForm.options" clearable placeholder="是否充值">
          <el-option
          v-for="item in options"
          :key="item.value"
          :label="item.label"
          :value="item.value">
          </el-option>
        </el-select> -->
        <!-- 选择框2 -->
        <!-- <el-select v-model="dataForm.type2" clearable placeholder="充值类型">
          <el-option
          v-for="item in options2"
          :key="item.value"
          :label="item.label"
          :value="item.value">
          </el-option>
        </el-select> -->
        <el-button @click="getDataList()">查询</el-button>
        <el-button @click="refresh()">重置</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        prop="id"
        header-align="center"
        align="center"
        label="序号">
      </el-table-column>
      <el-table-column
        prop="sn"
        header-align="center"
        align="center"
        label="订单号">
      </el-table-column>
      <el-table-column
        prop="uid"
        header-align="center"
        align="center"
        label="用户UID">
      </el-table-column>
      <el-table-column
        prop="mobile"
        header-align="center"
        align="center"
        label="手机号">
      </el-table-column>
      <el-table-column
        prop="username"
        header-align="center"
        align="center"
        label="昵称">
      </el-table-column>
      <el-table-column
        prop="goodsName"
        header-align="center"
        align="center"
        label="商品名称">
      </el-table-column>
      <el-table-column
        prop='image'
        header-align="center"
        align="center"
        label="商品图片">
        <template slot-scope="scope">
        <img :src="scope.row.image" id="imgsize">
        </template>
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="状态">
       <template slot-scope="scope">
        <div>
          {{ statuschange[scope.row.status] }}
        </div>
       </template>
      </el-table-column>
      <el-table-column
        prop="goodsPrice"
        header-align="center"
        align="center"
        label="市场价">
        <!-- <template slot-scope="scope">
          <div>
            <el-tag v-if="scope.row.paid == 1" type="success">已充值</el-tag>
            <el-tag v-else type="info">未充值</el-tag>
          </div>
        </template> -->
      </el-table-column>
      <el-table-column
        prop="flowPrice"
        header-align="center"
        align="center"
        label="结算价">
      </el-table-column>
      <el-table-column
        prop="num"
        header-align="center"
        align="center"
        label="数量">
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        width="180px"
        label="时间">
      </el-table-column>
      <!-- <el-table-column
        prop="type"
        header-align="center"
        align="center"
        label="类型">
        <template slot-scope="scope">
          <div>
            <el-tag v-if="scope.row.type == 0" >钱包充值</el-tag>
            <el-tag v-else type="warning">会员充值</el-tag>
          </div>
        </template>
      </el-table-column>
      <el-table-column
        prop="payTime"
        header-align="center"
        align="center"
        label="充值支付时间">
      </el-table-column>
      <el-table-column
        prop="addTime"
        header-align="center"
        align="center"
        label="充值时间">
      </el-table-column>
      <el-table-column
        prop="transactionId"
        header-align="center"
        align="center"
        label="支付生成订单号">
      </el-table-column>
      <el-table-column
        prop="outTradeNo"
        header-align="center"
        align="center"
        label="订单支付编号">
      </el-table-column> -->
      <!-- <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column> -->
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
  </div>
</template>

<script>
  import AddOrUpdate from './userrecharge-add-or-update'
  export default {
  data() {
      return {
        statuschange: {
          "TAKE": '待核销',
          "UNPAID": "未支付",
          "CHECKED":"已核销"
        },
        dataForm: {
          status:''
        },             
        notConvert:false,        // 需要驼峰转树形
        pageNumber: 1,           // 页号
        pageSize: 10,            // 页面大小
        sort: '',                // 排序字段
        order:'',                // 排序方式
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        options: [{
          value: 1,
          label: '已充值'
        }, {
          value: 0,
          label: '未充值'
        }],
        options2: [{
          value: 1,
          label: '会员充值'
        }, {
          value: 0,
          label: '钱包充值'
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
        this.dataForm={};
        this.pageNumber=1;
        this.pageSize=10;
        this.getDataList();
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/admin/pointOrder/list'),
          method: 'get',
          params: this.$http.adornParams({
            'memberId':this.dataForm.memberId,
            'phone':this.dataForm.phone,
            'nickName':this.dataForm.nickName,
            'status': this.dataForm.status,
            'pageNumber': this.pageNumber,
            'pageSize': this.pageSize,
            'sort': this.sort,
            'order': this.order,
            'notConvert':this.notConvert
          })
        }).then(({data}) => {
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
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageNumber = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle(val) {
        // console.log(val);
        this.pageNumber = val
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
            url: this.$http.adornUrl('/admin/userrecharge/delete'),
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
<style>
#imgsize {
  display: inline-block;
  width: 40px;
  height: 40px;
}
</style>
