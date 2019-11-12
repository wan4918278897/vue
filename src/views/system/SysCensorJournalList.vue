<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">

          <a-col :md="6" :sm="8">
            <a-form-item label="编写时间">
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
          <a @click="handleEdit(record)">查看</a>

        </span>

      </a-table>

    <!-- table区域-end -->

    <!-- 表单区域 -->
    <sys-censor-journal-modal ref="modalForm" @ok="modalFormOk"></sys-censor-journal-modal>
  </a-card>
</template>

<script>
  import SysCensorJournalModal from './modules/SysCensorJournalModal'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'

  export default {
    name: "SysCensorJournalList",
    mixins:[JeecgListMixin],
    components: {
      SysCensorJournalModal
    },
    data () {
      return {
        description: '个人日志管理页面',
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
            title: '姓名',
            align:"center",
            dataIndex: 'userName'
          },
          {
            title: '标题',
            align:"center",
            dataIndex: 'title'
          },
          {
            title: '日期',
            align:"center",
            dataIndex: 'writeTime'
          },

          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' },
          }
        ],
		url: {
          list: "/system/sysJournal/list",
          delete: "/system/sysJournal/delete",
          deleteBatch: "/system/sysJournal/deleteBatch",
          exportXlsUrl: "system/sysJournal/exportXls",
          importExcelUrl: "system/sysJournal/importExcel",
       },
    }
  },
  computed: {
    importExcelUrl: function(){
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
    }
  },
    methods: {
      onChange(date, dateString) {
        this.queryParam.checkDate=dateString;
      },
      searchReset()
      {
        this.selectTime=[];
        this.queryParam = {};
        this.loadData();
      },
    }
  }
</script>
<style scoped>
</style>