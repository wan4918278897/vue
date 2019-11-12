<!--
简报上报未审核界面
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
            <a-form-item
              label="审核状态">
              <a-select
                v-model="queryParam.reviewStatus"
                placeholder="选择状态">
                <a-select-option value="0">未审核</a-select-option>
                <a-select-option value="1">已审核</a-select-option>
                <a-select-option value="2">未通过</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item
              label="分类">
              <a-select
                v-model="queryParam.befType"
                placeholder="选择分类">
                <a-select-option value="0">工作动态</a-select-option>
                <a-select-option value="1">国内动态</a-select-option>
                <a-select-option value="2">典型案例</a-select-option>
                <a-select-option value="3">应急简报</a-select-option>
              </a-select>
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
      <a-button type="primary" icon="download" @click="handleExportXls('简报')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>

    </div>

    <!-- table区域-begin -->
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
          <a @click="handleEdit(record)"  v-if="record.reviewStatus==0||record.reviewStatus==2">编辑</a>

          <a-divider type="vertical" />

          <a-dropdown v-if="record.reviewStatus==0||record.reviewStatus==2">
            <a class="ant-dropdown-link" >更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
              <a-popconfirm title="确定重新上报?" @confirm="() => reApproved(record.id)" v-if="record.reviewStatus==2">
                  <a>重新上报</a>
                </a-popconfirm>
                </a-menu-item>
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
    <briefing-report-modal ref="modalForm" @ok="briefingModalFormOk"></briefing-report-modal>
  </a-card>
</template>

<script>
  import { getAction } from '@/api/manage'
  import BriefingReportModal from './modules/BriefingReportModal'
  import { BriefingListMixin } from '@/mixins/BriefingListMixin'

  export default {
    name: "BriefingReportList",
    mixins:[BriefingListMixin],
    components: {
      BriefingReportModal
    },
    data () {
      return {
        description: '简报管理页面',
        /*是否为上报人浏览*/
        isReporter:1,
        /*非上报人与审核人浏览*/
        isVisitor:0,
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
            title: '类型',
            align:"center",
            dataIndex: 'befType',
            customRender:function (text) {
              if(text=='0'){
                return "工作动态";
              }else if(text=="1"){
                return "国内动态";
              }else if(text=="2"){
                return "典型案例";
              } else if(text="3"){
                return "应急简报";
              }
            }
          },
          {
            title: '审核状态',
            align:"center",
            dataIndex: 'reviewStatus',
            customRender:function (text) {
              if(text=='0'){
                return "未审核";
              }else if(text=="1"){
                return "已通过";
              }else if(text=="2"){
                return "未通过";
              }
            }
          },
          {
            title: '发布单位',
            align:"center",
            dataIndex: 'departName'
          },

          {
            title: '上报时间',
            align:"center",
            dataIndex: 'reportTime'
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
          updateStatue: "emergencyduty/briefing/updateStatue",
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
        this.queryParam = {};
        //是否为上报人
        this.queryParam.isReporter='1';
        this.loadData();
      },

      reApproved: function (id) {
        let that=this;
        let params = {
          id: id,
          status: '0',
        };
        getAction(this.url.updateStatue, params).then((res) => {
          if (res.success) {
            that.$message.success("操作成功");
            that.$emit('ok');
          } else {
            that.$message.success("操作失败");
          }

        }).finally(() => {
          this.briefingModalFormOk();
        })

      },
    },
    created() {

      this.searchReset();
    },

  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>