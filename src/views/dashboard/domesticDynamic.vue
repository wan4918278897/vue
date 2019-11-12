<template>
  <a-table
    class="eventInforMationTable"
    ref="table"
    rowKey="id"
    border="true"
    :columns="columns"
    :dataSource="dataSource"
    :pagination="false"
  ></a-table>
</template>

<script>
import { filterObj } from '@/utils/util'
import { getUserList } from '@/api/api'
import { getAction } from '@/api/manage'
import { JeecgListMixin } from '@/mixins/JeecgListMixin'

export default {
  mixins: [JeecgListMixin],
  name: 'typicalCase',
  data() {
    return {
      title: '典型案例',
      queryParam: {},
      departIds: [],
      // 表头
      columns: [
        {
          title: '序号',
          dataIndex: '',
          key: 'rowIndex',
          width: 60,
          align: 'center',
          customRender: function(t, r, index) {
            return parseInt(index) + 1
          }
        },
        {
          title: '标题',
          align: 'center',
          dataIndex: 'title'
        },
        {
          title: '发布单位',
          align: 'center',
          dataIndex: 'departName'
        },
        {
          title: '发布时间',
          align: 'center',
          dataIndex: 'publishTime'
        }
      ],
      url: {
        list: '/emergencyduty/briefing/list'
      }
    }
  }
}
</script>
<style lang="less">
.eventInforMationTable {
  background: #fff;
}
.eventInforMationTable .ant-table-thead > tr > th,
.ant-table-tbody > tr > td {
  padding: 10px 16px;
}
</style>


