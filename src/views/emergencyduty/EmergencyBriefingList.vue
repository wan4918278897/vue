<!--
简报审核通过审核界面
-->
<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">

          <a-col :md="6" :sm="8">
            <a-form-item label="标题">
              <a-input placeholder="请输入标题" v-model="queryParam.title"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="发布时间">
              <a-range-picker @change="onChange" v-model="queryTime"/>
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
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        @change="handleTableChange">

        <span slot="action" slot-scope="text, record">
          <a @click="handleEdit(record)">查看</a>

        </span>

      </a-table>
    </div>
    <!-- table区域-end -->

    <!-- 表单区域 -->
    <briefing-modal ref="modalForm" @ok="briefingModalFormOk"></briefing-modal>
  </a-card>
</template>

<script>
  import BriefingModal from './modules/BriefingModal'
  import {BriefingListMixin} from '@/mixins/BriefingListMixin';

  export default {
    name: "EmergencyBriefingList",
    mixins: [BriefingListMixin],
    components: {
      BriefingModal
    },
    data () {
      return {
        description: '简报管理页面',
        /*审核状态*/
        dynamicStatus:1,
        /*动态类型*/
        befType:3,
        /*是否为上报人浏览*/
        isReporter:0,
        /*用户浏览*/
        isVisitor:1,
        queryTime:[],

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
            title: '发布单位',
            align:"center",
            dataIndex: 'departName'
          },
          {
            title: '发布时间',
            align:"center",
            dataIndex: 'publishTime'
          },

          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            scopedSlots: { customRender: 'action' },
          }
        ],
        url: {
          list: "/emergencyduty/briefing/list",
          delete: "/emergencyduty/briefing/delete",
          deleteBatch: "/emergencyduty/briefing/deleteBatch",
          exportXlsUrl: "emergencyduty/briefing/exportXls",
          importExcelUrl: "emergencyduty/briefing/importExcel",
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
        this.queryTime=[];
        this.queryParam = {};
        this.queryParam.reviewStatus=this.dynamicStatus;
        //是否为上报人
        this.queryParam.isReporter=this.isReporter;
        this.queryParam.befType=this.befType;
        console.log("queryParam",this.queryParam);
        this.loadData();
      },
      onChange(date, dateString) {
        this.queryParam.pubStartDate=dateString[0];
        this.queryParam.pubEndDate=dateString[1];
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