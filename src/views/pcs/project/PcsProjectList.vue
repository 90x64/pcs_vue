<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="项目名称">
              <a-input placeholder="请输入项目名称" v-model="queryParam.projectName"></a-input>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="项目编码">
              <a-input placeholder="请输入项目编码" v-model="queryParam.projectNo"></a-input>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        class="j-table-force-nowrap"
        @change="handleTableChange">

        <template slot="htmlSlot" slot-scope="text">
          <div v-html="text"></div>
        </template>
        <template slot="imgSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无图片</span>
          <img v-else :src="getImgView(text)" height="25px" alt="" style="max-width:80px;font-size: 12px;font-style: italic;"/>
        </template>
        <template slot="fileSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无文件</span>
          <a-button
            v-else
            :ghost="true"
            type="primary"
            icon="download"
            size="small"
            @click="downloadFile(text)">
            下载
          </a-button>
        </template>

        <span slot="action" slot-scope="text, record">
          <a @click="handleEdit(record)">编辑</a>

          <a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a @click="handleDetail(record)">详情</a>
              </a-menu-item>
              <a-menu-item>
                <a @click="showDetail(record.id)">明细</a>
              </a-menu-item>
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>

          <a-modal
            v-model="modalVisible"
            destroyOnClose
            maskClosable
            title=""
            width="1200px"
            :footer="null"
            centered
            @ok="() => (modalVisible = false)"
          >
            <template>
              <ProjectDetail :pid = "selectID"  v-model="projectDetail"></ProjectDetail>
            </template>
          </a-modal>

        </span>
      </a-table>
    </div>

    <pcs-project-modal ref="modalForm" @ok="modalFormOk"></pcs-project-modal>
  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import PcsProjectModal from './modules/PcsProjectModal'
  import {filterMultiDictText} from '@/components/dict/JDictSelectUtil'
  import ProjectDetail from './ProjectDetail'

  export default {
    name: 'PcsProjectList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      PcsProjectModal, ProjectDetail
    },
    data () {
      return {
        description: '项目信息管理管理页面',
        projectDetail: "",
        selectID: '',
        modalVisible: false,
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title:'项目名称',
            align:"center",
            dataIndex: 'projectName'
          },
          {
            title:'项目编码',
            align:"center",
            dataIndex: 'projectNo'
          },
          {
            title:'项目日期',
            align:"center",
            dataIndex: 'startingDate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'项目经理',
            align:"center",
            dataIndex: 'projectManager_dictText'
          },
          {
            title:'工程承建单位',
            align:"center",
            dataIndex: 'company_dictText'
          },
          {
            title:'工程实施单位',
            align:"center",
            dataIndex: 'constructionUnit_dictText'
          },
          {
            title:'施工日期',
            align:"center",
            dataIndex: 'construtionDate',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
          },
          {
            title:'联系电话',
            align:"center",
            dataIndex: 'contactNumber'
          },
          {
            title:'类型',
            align:"center",
            dataIndex: 'projectStatus'
          },
          {
            title:'管线',
            align:"center",
            dataIndex: 'pipeline'
          },
          {
            title:'站控',
            align:"center",
            dataIndex: 'station'
          },
          /*
          {
            title:'ip1',
            align:"center",
            dataIndex: 'ip1'
          },
          {
            title:'ip2',
            align:"center",
            dataIndex: 'ip2'
          },
          {
            title:'ip3',
            align:"center",
            dataIndex: 'ip3'
          },
          {
            title:'ip4',
            align:"center",
            dataIndex: 'ip4'
          }, */
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/pcsProject/list",
          delete: "/pcsProject/delete",
          deleteBatch: "pcsProject/deleteBatch",
          exportXlsUrl: "/pcsProject/exportXls",
          importExcelUrl: "pcsProject/importExcel",
          
        },
        dictOptions:{},
        modal: {
          pid: ''
        }
      }
    },
    created() {
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      },
    },
    methods: {
      initDictConfig(){
      },
      showDetail(pid) {
        this.modalVisible = true
        this.selectID = pid
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>