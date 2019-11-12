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
  name: 'staffList',
  data() {
    return {
      title: '事件信息',
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
          title: '事件标题',
          align: 'center',
          dataIndex: 'title'
        },

        {
          title: '事件性质',
          align: 'center',
          dataIndex: 'property'
        },
        {
          title: '事件类型',
          align: 'center',
          dataIndex: 'type'
        },
        {
          title: '事件级别',
          align: 'center',
          dataIndex: 'level'
        }
      ],
      url: {
        list: '/even/eventReport/publishList'
      },
    }
  }
}
</script>
<style lang="less">
.eventInforMationTable {
  background: #fff;
}
.eventInforMationTable .ant-table-thead > tr > th, .ant-table-tbody > tr > td {
  padding: 10px 16px;
}
</style>


