<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">

          <a-col :md="6" :sm="8">
            <a-form-item label="信息标题">
              <a-input placeholder="请输入信息标题" v-model="queryParam.foreTitle"></a-input>
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
      <a-button type="primary" icon="download" @click="handleExportXls('预测信息')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
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
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
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
    <forecastInfo-modal ref="modalForm" @ok="modalFormOk"></forecastInfo-modal>
  </a-card>
</template>

<script>
  import ForecastInfoModal from './modules/ForecastInfoModal'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'

  export default {
    name: "ForecastInfoList",
    mixins:[JeecgListMixin],
    components: {
      ForecastInfoModal
    },
    data () {
      return {
        description: '预测信息管理页面',
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
            title: '预测编号',
            align:"center",
            dataIndex: 'foreId'
          },
          {
            title: '信息标题',
            align:"center",
            dataIndex: 'foreTitle'
          },
          {
            title: '事件类型',
            align:"center",
            dataIndex: 'foreType'
          },
		   {
            title: '预测单位',
            align:"center",
            dataIndex: 'foreDep'
           },
          {
            title: '影响范围行政区划',
            align:"center",
            dataIndex: 'districtId'
          },
          {
            title: '专业预测内容',
            align:"center",
            dataIndex: 'content'
          },
		   {
            title: '上报单位',
            align:"center",
            dataIndex: 'reportDep'
           },
		   {
            title: '上报时间',
            align:"center",
            dataIndex: 'reportTime'
           },
		   {
            title: '保密等级',
            align:"center",
            dataIndex: 'securityLevel'
           },
          {
            title: '备注',
            align:"center",
            dataIndex: 'notes'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' },
          }
        ],
		url: {
          list: "/EventProcessing/forecastInfo/list",
          delete: "/EventProcessing/forecastInfo/delete",
          deleteBatch: "/EventProcessing/forecastInfo/deleteBatch",
          exportXlsUrl: "EventProcessing/forecastInfo/exportXls",
          importExcelUrl: "EventProcessing/forecastInfo/importExcel",
       },
    }
  },
  computed: {
    importExcelUrl: function(){
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
    }
  },
    methods: {
     
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>