<template>
  <a-card :bordered="false">
    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('1')">导出</a-button>
      <a-upload
        name="file"
        :showUploadList="false"
        :multiple="false"
        :headers="tokenHeader"
        :action="importExcelUrl"
        @change="handleImportExcel"
      >
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel">
            <a-icon type="delete"/>删除
          </a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px">
          批量操作
          <a-icon type="down"/>
        </a-button>
      </a-dropdown>
    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择
        <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
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
        :customRow="setRow"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        @change="handleTableChange"
      >
        <span slot="action" slot-scope="text, record">
          <a @click="modalEdit(record)">编辑</a>
          <a-divider type="vertical"/>
          <a-dropdown>
            <a class="ant-dropdown-link">
              更多
              <a-icon type="down"/>
            </a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>
      </a-table>
    </div>
    <!-- table区域-end -->
    <!-- 表单区域 -->
    <scheduleDefine-modal ref="modalForm" @ok="modalFormOk"></scheduleDefine-modal>
  </a-card>
</template>

<script>
import ScheduleDefineModal from './modules/ScheduleDefineModal'
import { JeecgListMixin } from '@/mixins/JeecgListMixin'
import { getAction } from '@/api/manage'

export default {
  name: 'ScheduleDefineList',
  mixins: [JeecgListMixin],
  components: {
    ScheduleDefineModal
  },
  data() {
    return {
      description: '班次定义',
      userName: [],
      id: '',
      // 表头
      columns: [
        {
          title: '#',
          dataIndex: '',
          key: 'rowIndex',
          width: 60,
          align: 'center',
          selectedUser: [],
          customRender: function(t, r, index) {
            return parseInt(index) + 1
          }
        },
        {
          title: '开始时间',
          align: 'center',
          dataIndex: 'startTime'
        },
        {
          title: '结束时间',
          align: 'center',
          dataIndex: 'endTime'
        },
        {
          title: '优先级',
          align: 'center',
          dataIndex: 'priority'
        },
        {
          title: '排班描述',
          align: 'center',
          dataIndex: 'scheduleDescribe'
        },
        {
          title: '状态',
          align: 'center',
          dataIndex: 'scheduleStatus'
        },
        {
          title: '班次名称',
          align: 'center',
          dataIndex: 'scheduleType_dictText'
        },
        {
          title: '操作',
          dataIndex: 'action',
          align: 'center',
          scopedSlots: { customRender: 'action' }
        }
      ],
      url: {
        list: '/scheduling/scheduleDefine/list',
        delete: '/scheduling/scheduleDefine/delete',
        deleteBatch: '/scheduling/scheduleDefine/deleteBatch',
        exportXlsUrl: 'scheduling/scheduleDefine/exportXls',
        importExcelUrl: 'scheduling/scheduleDefine/importExcel',
        queryDealList: '/scheduling/schedulePerson/findSchedulePerson'
      }
    }
  },
  computed: {
    importExcelUrl: function() {
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`
    }
  },
  created: function() {
    this.loadData()
  },
  methods: {
    modalEdit(record) {
      this.$refs.modalForm.open(record)
      this.$refs.modalForm.title = '编辑'
    }
  }
}
</script>
<style scoped>
</style>