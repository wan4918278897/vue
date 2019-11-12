<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">


          <a-col :md="6" :sm="8">
            <a-form-item label="标题">
              <a-input placeholder="请输入title" v-model="queryParam.title"></a-input>
            </a-form-item>
          </a-col>
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
          <a @click="handleEdit(record)">编辑</a>

          <a-divider type="vertical" />
          <a @click="handleDelete(record.id)">删除</a>
        </span>

      </a-table>
    </div>
    <!-- table区域-end -->

    <!-- 表单区域 -->
    <sysJournal-modal ref="modalForm" @ok="modalFormOk"></sysJournal-modal>
  </a-card>
</template>

<script>
  import SysJournalModal from './modules/SysJournalModal'
  import { BriefingListMixin } from '@/mixins/BriefingListMixin'

  export default {
    name: "SysJournalList",
    mixins:[BriefingListMixin],
    components: {
      SysJournalModal
    },
    data () {
      return {
        description: '个人日志管理页面',
        selectTime:'',
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
    },
  created()
  {
    this.searchReset();
  }
  }
</script>
<style scoped>
</style>