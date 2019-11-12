<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">

          <a-col :md="6" :sm="8">
            <a-form-item label="预案名称">
              <a-input placeholder="请输入预案名称" v-model="queryParam.name"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="预案状态">
              <a-select placeholder="请选择" v-model="queryParam.reserveStatues">
                <a-select-option value="0">未审核</a-select-option>
                <a-select-option value="1">已通过</a-select-option>
                <a-select-option value="2">未通过</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <template v-if="toggleSearchStatus">
            <a-col :md="6" :sm="8">
              <a-form-item label="预警分类">
                <a-select
                  placeholder="请选择"
                  v-model="queryParam.type"
                >
                  <a-select-option v-for="(item, key) in reserveType"
                                   :key="key"
                                   :value="item.id">{{item.name}}
                  </a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="发布单位">
                <a-input placeholder="请输入单位名称" v-model="queryParam.publishOrg"></a-input>
              </a-form-item>
            </a-col>
            <a-col :md="9" :sm="8">
              <a-form-item label="发布日期">
                <a-range-picker showTime v-model="queryTime">
                </a-range-picker>
              </a-form-item>
            </a-col>
          </template>
          <a-col :md="6" :sm="8">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="evenQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="evenReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a>
            </span>
          </a-col>

        </a-row>
      </a-form>
    </div>



    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{
        selectedRowKeys.length }}</a>项
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
          <a @click="handleEdit(record)">详情</a>
        </span>
      </a-table>
    </div>
    <!-- table区域-end -->

    <!-- 表单区域 -->
    <reserveInfo-detail-modal ref="modalForm" @ok="modalFormOk"></reserveInfo-detail-modal>
  </a-card>
</template>

<script>
  import ReserveInfoDetailModal from './modules/ReserveInfoDetailModal'
  import {JeecgListMixin} from '@/mixins/JeecgListMixin'
  import moment from 'moment'
  import {getAction,postAction} from '@/api/manage'

  export default {
    name: "ReservePublishList",
    mixins: [JeecgListMixin],
    components: {
      ReserveInfoDetailModal
    },
    data() {
      return {
        description: '预案信息管理页面',
        queryTime: [],
        reserveType: [],
        // 表头
        columns: [
          {
            title: '序号',
            dataIndex: '',
            key: 'rowIndex',
            width: 60,
            align: "center",
            customRender: function (t, r, index) {
              return parseInt(index) + 1;
            }
          },
          {
            title: '预案名称',
            align: "center",
            dataIndex: 'name'
          },
          {
            title: '预案分类',
            align: "center",
            dataIndex: 'type'
          },
          {
            title: '发布时间',
            align: "center",
            dataIndex: 'date',
            customRender(text) {
              return moment(text).format('YYYY-MM-DD')
            }
          },
          {
            title: '预案状态',
            align: "center",
            dataIndex: 'reserveStatues',
            customRender(text) {
              if (text == '0') {
                return "未审核";
              } else if (text == "1") {
                return "已通过";
              } else if (text == "2") {
                return "未通过";
              }
            }
          },
          {
            title: '发布单位',
            align: "center",
            dataIndex: 'departName'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align: "center",
            scopedSlots: {customRender: 'action'},
          }
        ],
        url: {
          list: "/forecastInfo/reserveInfo/publishList",
          getReserveSetting: "/forecastInfo/reserveInfo/getReserveSetting",
          approved: '/forecastInfo/reserveInfo/approved'
        },
      }
    },
    created() {
      getAction(this.url.getReserveSetting).then(res => {
        this.reserveType = res;
      })
    },
    methods: {
      evenQuery() {
        if (this.queryTime.length == 2) {
          this.queryParam.beginDate = moment(this.queryTime[0]).format('YYYY-MM-DD');
          this.queryParam.endDate = moment(this.queryTime[1]).format('YYYY-MM-DD');
        }
        this.searchQuery()
      },
      evenReset() {
        this.queryTime = [];
        this.searchReset();
      }
    }
  }
</script>
<style scoped>
</style>