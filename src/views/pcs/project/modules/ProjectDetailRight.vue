<template>
  <a-card class="j-address-list-right-card-box" :loading="cardLoading" :bordered="false">


    <!-- table区域-begin -->
    <div>
      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        class="j-table-force-nowrap"
        @change="handleTableChange">
      </a-table>
    </div>
  </a-card>
</template>

<script>
  import { getAction } from '@/api/manage'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'

  export default {
    name: 'ProjectRecordList',
    mixins:[JeecgListMixin],
    components: {},
    props: ['value'],
    data () {
      return {
        description: '工程数据记录表管理页面',
        cardLoading: true,
        positionInfo: {},
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
            title:'文件名称',
            align:"center",
            dataIndex: 'fileName'
          },
          {
            title:'文件版本号',
            align:"center",
            dataIndex: 'fileVersion'
          },
          {
            title:'文件md5',
            align:"center",
            dataIndex: 'fileMd5'
          },
          {
            title:'文件存储路径',
            align:"center",
            dataIndex: 'fileUrl'
          },
          {
            title:'备注',
            align:"center",
            dataIndex: 'remarks'
          },
          {
            title:'更新日期',
            align:"center",
            dataIndex: 'updateTime'
          }
        ],
        url: {
          list: "/projectRecord/list"

        }
      }
    },
    watch: {
      value: {
        immediate: true,
        handler(modelId) {
          this.dataSource = []
          this.loadData(1, modelId)
        }
      }
    },
    created() {},
    methods: {

      loadData(pageNum, modelId) {
        this.loading = true
        if (pageNum === 1) {
          this.ipagination.current = 1
        }

        if (!modelId) {
          getAction(this.url.list, {
            ...this.getQueryParams()
          }).then((res) => {
            if (res.success) {
              this.dataSource = res.result.records
              this.ipagination.total = res.result.total
            }
          }).finally(() => {
            this.loading = false
            this.cardLoading = false
          })
          // update-end- --- author:wangshuai ------ date:20200102 ---- for:传过来的部门编码为空全查
        }else{
          //加载数据 若传入参数1则加载第一页的内容
          getAction(this.url.list, {
            modelId,
            ...this.getQueryParams()
          }).then((res) => {
            if (res.success) {
              this.dataSource = res.result.records
              this.ipagination.total = res.result.total
            }
          }).finally(() => {
            this.loading = false
            this.cardLoading = false
          })
        }
      },

      handleTableChange(pagination, filters, sorter) {
        this.ipagination = pagination
        this.loadData(null, this.value)
      }

    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>