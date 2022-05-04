<template>
  <div class="page-header-index-wide">
    <a-row :gutter="24">
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="线索总量" :total="warningInfo.total">
          <a-tooltip title="线索总数据量" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <!--          <div>-->
          <!--            <trend flag="up" style="margin-right: 16px;">-->
          <!--              <span slot="term">周同比</span>-->
          <!--              12%-->
          <!--            </trend>-->
          <!--            <trend flag="down">-->
          <!--              <span slot="term">日同比</span>-->
          <!--              11%-->
          <!--            </trend>-->
          <!--          </div>-->
          <!--          <template slot="footer">日均销售额<span>￥ 234.56</span></template>-->
        </chart-card>
      </a-col>
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="已归档" :total="warningInfo.archived">
          <a-tooltip title="线索已归档数据" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <!--          <div>-->
          <!--            <mini-area />-->
          <!--          </div>-->
          <!--          <template slot="footer">日订单量<span> {{ '1234' | NumberFormat }}</span></template>-->
        </chart-card>
      </a-col>
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="无调查结论" :total="warningInfo.noVerified ">
          <a-tooltip title="线索无调查结论数据" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <!--          <div>-->
          <!--            <mini-bar :height="40" />-->
          <!--          </div>-->
          <!--          <template slot="footer">转化率 <span>60%</span></template>-->
        </chart-card>
      </a-col>
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="无回访情况" :total="warningInfo.noArchived">
          <a-tooltip title="线索无回访情况数据" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <!--          <div>-->
          <!--            <mini-progress color="rgb(19, 194, 194)" :target="80" :percentage="78" :height="8" />-->
          <!--          </div>-->
          <!--          <template slot="footer">-->
          <!--            <trend flag="down" style="margin-right: 16px;">-->
          <!--              <span slot="term">同周比</span>-->
          <!--              12%-->
          <!--            </trend>-->
          <!--            <trend flag="up">-->
          <!--              <span slot="term">日环比</span>-->
          <!--              80%-->
          <!--            </trend>-->
          <!--          </template>-->
        </chart-card>
      </a-col>
    </a-row>

    <a-card :loading="loading" :bordered="false" :body-style="{padding: '0'}">
      <div class="salesCard">
        <a-tabs default-active-key="1" size="large" :tab-bar-style="{marginBottom: '24px', paddingLeft: '16px'}">
          <div class="extra-wrapper" slot="tabBarExtraContent">
            <div class="extra-item">
              <a>今日</a>
              <a>本周</a>
              <a>本月</a>
              <a>本年</a>
            </div>
            <a-range-picker :style="{width: '256px'}" />
          </div>
          <a-tab-pane loading="true" tab="线索情况" key="1">
            <a-row>
              <a-col :sm="24" :xs="24">
                <bar title="线索数量" :dataSource="barData" yaxisText='数量' :height='400'/>
              </a-col>
              <!--              <a-col :xl="8" :lg="12" :md="12" :sm="24" :xs="24">-->
              <!--                <rank-list title="门店销售排行榜" :list="rankList"/>-->
              <!--              </a-col>-->
            </a-row>
          </a-tab-pane>
          <a-tab-pane tab="办理情况" key="2">
            <a-row>
              <a-col :xl="24" :lg="24" :md="24" :sm="24" :xs="24">
                <bar-multid title="办案数量" :height='400' :data-source='barMultid.dataSource' :fields='barMultid.fields' />
              </a-col>
              <!--              <a-col :xl="8" :lg="12" :md="12" :sm="24" :xs="24">-->
              <!--                <rank-list title="门店销售排行榜" :list="rankList"/>-->
              <!--              </a-col>-->
            </a-row>
          </a-tab-pane>
        </a-tabs>
      </div>
    </a-card>

    <!--    <a-row>-->
    <!--      <a-col :span="24">-->
    <!--        <a-card :loading="loading" :bordered="false" title="最近一周访问量统计" :style="{ marginTop: '24px' }">-->
    <!--          <a-row>-->
    <!--            <a-col :span="6">-->
    <!--              <head-info title="今日IP" :content="loginfo.todayIp"></head-info>-->
    <!--            </a-col>-->
    <!--            <a-col :span="2">-->
    <!--              <a-spin class='circle-cust'>-->
    <!--                <a-icon slot="indicator" type="environment" style="font-size: 24px"  />-->
    <!--              </a-spin>-->
    <!--            </a-col>-->
    <!--            <a-col :span="6">-->
    <!--              <head-info title="今日访问" :content="loginfo.todayVisitCount"></head-info>-->
    <!--            </a-col>-->
    <!--            <a-col :span="2">-->
    <!--              <a-spin class='circle-cust'>-->
    <!--                <a-icon slot="indicator" type="team" style="font-size: 24px"  />-->
    <!--              </a-spin>-->
    <!--            </a-col>-->
    <!--            <a-col :span="6">-->
    <!--              <head-info title="总访问量" :content="loginfo.totalVisitCount"></head-info>-->
    <!--            </a-col>-->
    <!--            <a-col :span="2">-->
    <!--              <a-spin class='circle-cust'>-->
    <!--                <a-icon slot="indicator" type="rise" style="font-size: 24px"  />-->
    <!--              </a-spin>-->
    <!--            </a-col>-->
    <!--          </a-row>-->
    <!--          <line-chart-multid :fields="visitFields" :dataSource="visitInfo"></line-chart-multid>-->
    <!--        </a-card>-->
    <!--      </a-col>-->
    <!--    </a-row>-->
  </div>
</template>

<script>
import ChartCard from '@/components/ChartCard'
import ACol from "ant-design-vue/es/grid/Col"
import ATooltip from "ant-design-vue/es/tooltip/Tooltip"
import MiniArea from '@/components/chart/MiniArea'
import MiniBar from '@/components/chart/MiniBar'
import MiniProgress from '@/components/chart/MiniProgress'
import RankList from '@/components/chart/RankList'
import Bar from '@/components/chart/Bar'
import LineChartMultid from '@/components/chart/LineChartMultid'
import HeadInfo from '@/components/tools/HeadInfo.vue'

import Trend from '@/components/Trend'
import { getLoginfo,getVisitInfo } from '@/api/api'
import { getAction } from '@/api/manage'
import BarMultid from '@comp/chart/BarMultid'


const rankList = []
for (let i = 0; i < 7; i++) {
  rankList.push({
    name: '白鹭岛 ' + (i+1) + ' 号店',
    total: 1234.56 - i * 100
  })
}
export default {
  name: "IndexBdc",
  components: {
    BarMultid,
    ATooltip,
    ACol,
    ChartCard,
    MiniArea,
    MiniBar,
    MiniProgress,
    RankList,
    Bar,
    Trend,
    LineChartMultid,
    HeadInfo
  },
  data() {
    return {
      loading: true,
      center: null,
      rankList,
      barData: [],
      loginfo:{},
      visitFields:['ip','visit'],
      visitInfo:[],
      indicator: <a-icon type="loading" style="font-size: 24px" spin />,
      warningInfo: [],
      barMultid: {
        dataSource: [],
        fields: []
      }
    }
  },
  created() {
    setTimeout(() => {
      this.loading = !this.loading
    }, 1000)
    this.initLogInfo();
  },
  methods: {
    initLogInfo () {
      getAction('/inspector/dcWireCenter/warningInfo', {}).then(res=>{
        if(res.success && res.result){
          this.warningInfo = res.result
        }
      })
      getAction('/inspector/dcWireCenter/warningStatistics', {}).then(res=>{
        if(res.success && res.result){
          this.barData = res.result
        }
      })
      getAction('/inspector/dcWireCenter/handlingSituation', {}).then(res=>{
        if(res.success && res.result){
          this.barMultid.dataSource = res.result.dataSource
          this.barMultid.fields = res.result.fields
          console.log(this.barMultid)
        }
      })

      // getLoginfo(null).then((res)=>{
      //   if(res.success){
      //     Object.keys(res.result).forEach(key=>{
      //       res.result[key] =res.result[key]+""
      //     })
      //     this.loginfo = res.result;
      //   }
      // })
      // getVisitInfo().then(res=>{
      //   if(res.success){
      //      this.visitInfo = res.result;
      //    }
      //  })
    },
  }
}
</script>

<style lang="less" scoped>
.circle-cust{
  position: relative;
  top: 28px;
  left: -100%;
}
.extra-wrapper {
  line-height: 55px;
  padding-right: 24px;

  .extra-item {
    display: inline-block;
    margin-right: 24px;

    a {
      margin-left: 24px;
    }
  }
}

/* 首页访问量统计 */
.head-info {
  position: relative;
  text-align: left;
  padding: 0 32px 0 0;
  min-width: 125px;

  &.center {
    text-align: center;
    padding: 0 32px;
  }

  span {
    color: rgba(0, 0, 0, .45);
    display: inline-block;
    font-size: .95rem;
    line-height: 42px;
    margin-bottom: 4px;
  }
  p {
    line-height: 42px;
    margin: 0;
    a {
      font-weight: 600;
      font-size: 1rem;
    }
  }
}
</style>