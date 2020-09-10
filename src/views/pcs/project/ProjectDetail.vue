<template>
  <a-row type="flex" :gutter="16">
    <a-col :md="5" :sm="24">
      <address-list-left :pid2="pid" v-model="projectDetail"/>
    </a-col>
    <a-col :md="24-5" :sm="24">
      <address-list-right v-model="projectDetail"/>
    </a-col>
  </a-row>
</template>

<script>
  import AddressListLeft from './modules/ProjectDetailLeft'
  import AddressListRight from './modules/ProjectDetailRight'

  export default {
    name: 'ProjectDetail',
    props: ['pid'],
    components: { AddressListLeft, AddressListRight },
    data() {
      return {
        description: '项目详情',
        projectDetail: '',
        pid2: ''
      }
    },
    methods: {}
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>