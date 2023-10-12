<template>
  <div class="divBox">
      <el-row :gutter="24" class="dashboard-console-grid">
        
        <el-col v-bind="grid" class="ivu-mb">
          <el-card :bordered="false">
            <router-link :to="{ path: '/admin-post' }">
              <i class="el-icon-message" style="color: #ff9c6e" />
              <p>帖子管理</p>
            </router-link>
          </el-card>
        </el-col>
        <el-col v-bind="grid" class="ivu-mb">
          <el-card :bordered="false">
            <router-link :to="{ path: '/admin-topic' }">
              <i class="el-icon-document" style="color: #5cdbd3" />
              <p>圈子管理</p>
            </router-link>
          </el-card>
        </el-col>
        <el-col v-bind="grid" class="ivu-mb">
          <el-card :bordered="false">
            <router-link :to="{ path: '/admin-comment' }">
              <i class="el-icon-picture" style="color: #5cdbd3" />
              <p>评论管理</p>
            </router-link>
          </el-card>
        </el-col>

         <el-col v-bind="grid" class="ivu-mb">
          <el-card :bordered="false">
            <router-link :to="{ path: '/admin-user' }">
              <i class="el-icon-news" style="color: #95de64" />
              <p>会员管理</p>
            </router-link>
          </el-card>
        </el-col>
       
        <el-col v-bind="grid" class="ivu-mb">
          <el-card :bordered="false">
            <router-link :to="{ path: '/admin-message' }">
              <i class="el-icon-bell" style="color: #ff85c0" />
              <p>消息管理</p>
            </router-link>
          </el-card>
        </el-col>
        <el-col v-bind="grid" class="ivu-mb">
          <el-card :bordered="false">
            <router-link :to="{ path: '/admin-system' }">
              <i class="el-icon-question" style="color: #ffd666" />
              <p>配置管理</p>
            </router-link>
          </el-card>
        </el-col>
        <el-col v-bind="grid" class="ivu-mb">
          <el-card :bordered="false">
            <router-link :to="{ path: '/admin-link' }">
              <i class="el-icon-printer" style="color: #ff85c0" />
              <p>轮播管理</p>
            </router-link>
          </el-card>
        </el-col>
        <el-col v-bind="grid" class="ivu-mb">
          <el-card :bordered="false">
            <router-link :to="{ path: '/admin-report' }">
              <i class="el-icon-goods" style="color: #69c0ff" />
              <p>举报管理</p>
            </router-link>
          </el-card>
        </el-col>
      </el-row>
    </div>
</template>
<script>

export default {

  data() {
    return {
      count: { todayPrice: 0, todayCount: 0, proPrice: 0, proCount: 0,
        monthPrice: 0, monthCount: 0, lastWeekCount: 0, lastWeekPrice: 0 },
        grid: {
        xl: 3,
        lg: 3,
        md: 6,
        sm: 8,
        xs: 8,
      },
    }
  },

}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
.panel-group {
  margin-top: 18px;
  .card-panel-col{
    margin-bottom: 32px;
  }
  .card-panel {
    height: 108px;
    font-size: 12px;
    position: relative;
    overflow: hidden;
    cursor: pointer;
    color: #666;
    background: #fff;
    box-shadow: 4px 4px 40px rgba(0, 0, 0, .05);
    border-color: rgba(0, 0, 0, .05);



    .icon-people {
      color: #40c9c6;
    }
    .icon-message {
      color: #36a3f7;
    }
    .icon-money {
      color: #f4516c;
    }
    .icon-shopping {
      color: #34bfa3
    }
    .card-panel-icon-wrapper {
      float: left;
      margin: 14px 0 0 14px;
      padding: 16px;
      transition: all 0.38s ease-out;
      border-radius: 6px;
    }
    .card-panel-icon {
      float: left;
      font-size: 48px;
    }
    .card-panel-description {
      float: right;
      font-weight: bold;
      margin: 26px;
      margin-left: 0px;
      .card-panel-text {
        line-height: 18px;
        color: rgba(0, 0, 0, 0.45);
        font-size: 16px;
        margin-bottom: 12px;
      }
      .card-panel-num {
        font-size: 20px;
      }
    }
  }
}


.ivu-mb {
  margin-bottom: 10px;
}
// .divBox {
//   // padding: 0 20px !important;
// }

.dashboard-console-grid {
  text-align: center;
  .ivu-card-body {
    padding: 0;
  }
  i {
    font-size: 32px;
  }
  a {
    display: block;
    color: inherit;
  }
  p {
    margin-top: 8px;
  }
}
</style>
