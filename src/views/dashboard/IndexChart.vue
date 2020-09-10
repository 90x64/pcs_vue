<template>
  <div class="page-header-index-wide">
    <a-row :gutter="24">
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="数据采集点" total="38">
          <a-tooltip title="数据采集点" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <div>
            <trend flag="up" style="margin-right: 16px;">
              <span slot="term">周同比</span>
              1%
            </trend>
            <trend flag="down">
              <span slot="term">日同比</span>
              1%
            </trend>
          </div>
          <template slot="footer">日均采集点<span> 6</span></template>
        </chart-card>
      </a-col>
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="工程文件" :total="33 | NumberFormat">
          <a-tooltip title="工程文件" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <div>
            <mini-bar :height="40" />
          </div>
          <template slot="footer">日均上传量 <span> 7</span></template>
        </chart-card>
      </a-col>
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="SCADA点" total="24">
          <a-tooltip title="SCADA点" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <div>
            <trend flag="up" style="margin-right: 16px;">
              <span slot="term">周同比</span>
              1%
            </trend>
            <trend flag="down">
              <span slot="term">日同比</span>
              1%
            </trend>
          </div>
          <template slot="footer">日均采集点<span> 11</span></template>
        </chart-card>
      </a-col>
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }">
        <chart-card :loading="loading" title="工程画面" :total="7 | NumberFormat">
          <a-tooltip title="工程画面" slot="action">
            <a-icon type="info-circle-o" />
          </a-tooltip>
          <div>
            <mini-area />
          </div>
          <template slot="footer">日工程画面<span> {{ '2' | NumberFormat }}</span></template>
        </chart-card>
      </a-col>

    </a-row>

    <a-row>
      <a-col :span="24">
        <a-card :loading="loading" :bordered="false" title="工程分布" :style="{ marginTop: '24px' }">
          <bar title="" :dataSource="project" :height="200"/>
        </a-card>
      </a-col>
    </a-row>

    <a-row>
      <a-col :span="24">
        <a-card :loading="loading" :bordered="false" title="版本统计" :style="{ marginTop: '24px' }">
          <line-chart-multid :fields="visitFields" :dataSource="version"></line-chart-multid>
        </a-card>
      </a-col>
    </a-row>
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
  import Radar from '@/components/chart/Radar'

  import Trend from '@/components/Trend'
  import { getLoginfo,getVisitInfo } from '@/api/api'

  const rankList = []
  for (let i = 0; i < 7; i++) {
    rankList.push({
      name: '白鹭岛 ' + (i+1) + ' 号店',
      total: 1234.56 - i * 100
    })
  }
  const barData = []
  for (let i = 0; i < 12; i += 1) {
    barData.push({
      x: `${i + 1}月`,
      y: Math.floor(Math.random() * 1000) + 200
    })
  }
  export default {
    name: "IndexChart",
    components: {
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
      HeadInfo,
      Radar
    },
    data() {
      return {
        loading: true,
        center: null,
        rankList,
        barData,
        project: [
          {
            "x": "工程A",
            "y": 10
          },
          {
            "x": "工程B",
            "y": 28
          },
          {
            "x": "工程C",
            "y": 75
          }
        ],
        version: [
                    { "type": "2019年9月", "version": 1 },
                    { "type": "2019年10月", "version": 3 },
                    { "type": "2019年11月", "version": 6 },
                    { "type": "2019年12月", "version": 7 },
                    { "type": "2020年1月", "version": 6 },
                    { "type": "2020年2月", "version": 9 },
                    { "type": "2020年3月", "version": 14 },
                    { "type": "2020年4月", "version": 18 },
                    { "type": "2020年5月", "version": 21 },
                    { "type": "2020年6月", "version": 25 },
                    { "type": "2020年7月", "version": 26 },
                    { "type": "2020年8月", "version": 23 },
                    { "type": "2020年9月", "version": 18 }
                  ],
        loginfo:{},
        visitFields:["version"],
        visitInfo:[],
        indicator: <a-icon type="loading" style="font-size: 24px" spin />
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
        getLoginfo(null).then((res)=>{
          if(res.success){
            Object.keys(res.result).forEach(key=>{
              res.result[key] =res.result[key]+""
            })
            this.loginfo = res.result;
          }
        })
        getVisitInfo().then(res=>{
          if(res.success){
             this.visitInfo = res.result;
           }
         })
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