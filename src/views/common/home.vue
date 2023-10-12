<template>
  <div class="mod-home">
    <h2>数据概览</h2>

    <!--头部-->
    <base-info ref="baseInfo"/>
    <!-- 快捷入口 -->
    <panel-group-t />
    <!-- 分析统计 -->
      <div>
        <el-card :bordered="false" dis-hover>
          <div slot="header">
            <div>
              <i class="el-icon-printer" style="color: #95de64" />
              <span>数据统计</span>
            </div>
          </div>
          <el-row :gutter="24">
            
            <el-col
              :xl="8"
              :lg="8"
              :md="24"
              :sm="24"
              :xs="24"
              class="ivu-mb mb10 dashboard-console-visit"
            >
              <span class="ivu-pl-8">近30天发帖数</span>
            
              <bar-chart />
            </el-col>
            <el-col
              :xl="8"
              :lg="8"
              :md="24"
              :sm="24"
              :xs="24"
              class="ivu-mb mb10 dashboard-console-visit"
            >
              <span class="ivu-pl-8">近30天新增用户数</span>

              <pie-chart />
            </el-col>
            <el-col
              :xl="8"
              :lg="8"
              :md="24"
              :sm="24"
              :xs="24"
              class="ivu-mb mb10 dashboard-console-visit"
            >
              <span class="ivu-pl-8">本月交易额</span>

              <m-chart />
            </el-col>
          </el-row>
        </el-card>
      </div>
  </div>
</template>

<script>
import baseInfo from "./components/baseInfo";
import PanelGroupT from "./components/PanelGroupT";
import PieChart from "./components/BarChartT";
import MChart from "./components/BarChartM";
import BarChart from "./components/BarChart";
export default {
  components: { 
    baseInfo,
    PanelGroupT,
    PieChart,
    BarChart,
    MChart
    },
  data() {
    return {

    };
  },

};
</script>

<style>
.mod-home {
  line-height: 1.5;
}
</style>

