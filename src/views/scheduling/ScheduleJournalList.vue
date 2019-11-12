<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">

          <a-col :md="6" :sm="8">
            <a-form-item label="值班日期">
              <a-date-picker @change="onChange" v-model="queryParam.selectTime" />
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8" >
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>

            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>

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
        @change="handleTableChange">

        <span slot="action" slot-scope="text, record">
          <a @click="modalEdit(record)">编辑</a>

          <a-divider type="vertical" />
          <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
            <a>删除</a>
          </a-popconfirm>
        </span>

      </a-table>
    </div>
    <!-- table区域-end -->

    <!-- 表单区域 -->
    <scheduleJournal-modal ref="modalForm" @ok="modalFormOk"></scheduleJournal-modal>
  </a-card>
</template>

<script>
  import ScheduleJournalModal from './modules/ScheduleJournalModal'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'

  export default {
    name: "ScheduleJournalList",
    mixins:[JeecgListMixin],
    components: {
      ScheduleJournalModal
    },
    data () {
      return {
        description: '值班日志管理页面',
        selectTime:[],
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
            title: '标题',
            align:"center",
            dataIndex: 'title'
          },
		   {
            title: '值班日期',
            align:"center",
            dataIndex: 'dutyDate'
           },
          {
            title: '班次',
            align: 'center',
            dataIndex: 'scheduleType',
            customRender: function (text) {
              if (text == 'a') {
                return '早班'
              } else if (text == 'b') {
                return '中班'
              } else if (text == 'c') {
                return '晚班'
              }
            }
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' },
          }
        ],
		url: {
          list: "/scheduling/scheduleJournal/list",
          delete: "/scheduling/scheduleJournal/delete",
          deleteBatch: "/scheduling/scheduleJournal/deleteBatch",
          exportXlsUrl: "scheduling/scheduleJournal/exportXls",
          importExcelUrl: "scheduling/scheduleJournal/importExcel",
       },
    }
  },
  computed: {
    importExcelUrl: function(){
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
    }
  },
    methods: {

      modalEdit(record) {
        this.$refs.modalForm.open(record)

        this.$refs.modalForm.title = '编辑'

      },
      searchReset()
      {
        this.selectTime=[];
        this.queryParam = {};
        //是否为上报人
        this.queryParam.isReporter='1';
        this.loadData();
      },
      onChange(date, dateString) {
        this.queryParam.checkDate=dateString;
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>