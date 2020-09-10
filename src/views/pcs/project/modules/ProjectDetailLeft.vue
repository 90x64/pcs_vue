<template>
  <a-card :loading="cardLoading" :bordered="false" style="height: 100%;">
    <a-spin :spinning="loading">

      <a-tree
        showLine
        checkStrictly
        :expandedKeys.sync="expandedKeys"
        :selectedKeys="selectedKeys"
        :dropdownStyle="{maxHeight:'200px',overflow:'auto'}"
        :treeData="treeDataSource"
        @select="handleTreeSelect"
      />
    </a-spin>
  </a-card>
</template>

<script>
  import { queryModelTreeList, searchModelByKeywords } from '@/api/api'

  export default {
    name: 'ProjectDetailLeft',
    props: ['value', 'pid2'],
    temp: '',
    data() {
      return {
        cardLoading: true,
        loading: false,
        treeDataSource: [],
        selectedKeys: [],
        expandedKeys: []
      }
    },
    created() {
      this.queryTreeData()
    },
    watch: {
      pid2: {
        immediate: true,
        handler(projectId) {
          console.log(this.pid2);
          if (this.temp == undefined) {
            this.temp = projectId
            this.dataSource = []
            this.handleSearch(this.temp)
          }
        }
      }
    },
    methods: {

      queryTreeData(keyword) {
        this.commonRequestThen(queryModelTreeList({
          projectId: keyword ? keyword : undefined
        }))
      },

      handleSearch(value) {
        if (value) {
          this.commonRequestThen(searchModelByKeywords({ keyWord: value }))
        } else {
          this.queryTreeData()
        }
      },
      handleTreeSelect(selectedKeys, event) {
        if (selectedKeys.length > 0 && this.selectedKeys[0] !== selectedKeys[0]) {
          this.selectedKeys = [selectedKeys[0]]
          let modelId = event.node.dataRef.id
          this.emitInput(modelId)
        }
      },

      emitInput(modelId) {
        this.$emit('input', modelId)
      },

      commonRequestThen(promise) {
        this.loading = true
        promise.then(res => {
          if (res.success) {
            this.treeDataSource = res.result
          } else {
            this.$message.warn(res.message)
            console.error('工程数据模型查询失败:', res)
          }
        }).finally(() => {
          this.loading = false
          this.cardLoading = false
        })
      },

    }
  }
</script>
<style>
  .j-address-list-right-card-box .ant-table-placeholder {
    min-height: 46px;
  }
</style>
<style scoped>
  .j-address-list-right-card-box {
    height: 100%;
    min-height: 300px;
  }
</style>