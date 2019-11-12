<!--
简报审核未审核界面
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
          <a @click="handleEdit(record)">查看</a>
          <a-divider type="vertical" />
            <a-popconfirm title="确定过审吗?" @confirm="() => approved(record.id)">
              <a>过审</a>
            </a-popconfirm>
          <a-divider type="vertical" />
          <a-popconfirm title="确定回绝吗?" @confirm="() => notApproved(record.id)">
              <a>回绝</a>
          </a-popconfirm>

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
    <briefing-review-modal ref="modalForm" @ok="briefingModalFormOk"></briefing-review-modal>
  </a-card>
</template>

<script>
  import BriefingReviewModal from './modules/BriefingReviewModal'
  import { BriefingListMixin } from '@/mixins/BriefingListMixin'
  import {postAction, getAction} from '@/api/manage'

  export default {
    name: "BriefingReviewList",
    mixins: [BriefingListMixin],
    components: {
      BriefingReviewModal
    },
    data() {
      return {
        description: '简报管理页面',
        dynamicStatus: 0,
        /*是否为上报人浏览*/
        isReporter:0,
        /*用户浏览*/
        isVisitor:0,
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key: 'rowIndex',
            width: 60,
            align: "center",
            customRender: function (t, r, index) {
              return parseInt(index) + 1;
            }
          },
          {
            title: '标题',
            align: "center",
            dataIndex: 'title'
          },
          {
            title: '分类',
            align: "center",
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
            title: '发布单位',
            align: "center",
            dataIndex: 'departName'
          },
          {
            title: '上报人',
            align: "center",
            dataIndex: 'realname'
          },
          {
            title: '上报时间',
            align: "center",
            dataIndex: 'reportTime'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align: "center",
            scopedSlots: {customRender: 'action'},
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
      importExcelUrl: function () {
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    methods: {
      briefingModalFormOk: function () {
        //this.queryParam.reviewStatus=1;
        this.loadData();


      },
      approved: function (id) {
        let that=this
        let params = {
          id: id,
          status: '1',
        };
        console.log(params);
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
      notApproved: function (id) {
        let params = {
          id: id,
          status: '2',
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
      searchReset()
      {
        this.queryParam = {};
        //审核状态为未审核
        this.queryParam.reviewStatus=this.dynamicStatus;
        //是否为上报人
        this.queryParam.isReporter='0';
        this.loadData();
      },
    },
    created() {
      this.searchReset();
    },


  }
</script>
<style scoped>
</style>