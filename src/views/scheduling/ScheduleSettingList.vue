<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">
          <a-col :md="4" :sm="4">
            <a-form-item label="人数">
              <a-input placeholder="请输入人数" v-model="queryParam.num"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button
                type="primary"
                @click="searchReset"
                icon="reload"
                style="margin-left: 8px"
              >重置</a-button>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-popconfirm title="是否生成新的排班数据?" @confirm="() => autSchedule(111)">
        <a-button type="danger" style="float: right;">自动排班</a-button>
      </a-popconfirm>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel">
            <a-icon type="delete"/>
            删除
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
          <a-menu slot="overlay">
            <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
              <a>删除</a>
            </a-popconfirm>
          </a-menu>
        </span>
      </a-table>
    </div>
    <!-- table区域-end -->

    <!-- 表单区域 -->
    <scheduleSetting-modal ref="modalForm" @ok="modalFormOk" :timeList="timeList"></scheduleSetting-modal>
  </a-card>
</template>

<script>
  import ScheduleSettingModal from './modules/ScheduleSettingModal'
  import {JeecgListMixin} from '@/mixins/JeecgListMixin'
  import {getAction, postAction} from '@/api/manage'

  export default {
    name: 'ScheduleSettingList',
    mixins: [JeecgListMixin],
    components: {
      ScheduleSettingModal
    },
    data() {
      return {
        description: '1管理页面',
        // schedulingName: [],
        timeList: [
          {value: '1', key: '周一'},
          {value: '2', key: '周二'},
          {value: '3', key: '周三'},
          {value: '4', key: '周四'},
          {value: '5', key: '周五'},
          {value: '6', key: '周六'},
          {value: '7', key: '周日'}
        ],
        // 表头
        columns: [
          {
            title: '序号',
            dataIndex: '',
            key: 'rowIndex',
            width: 60,
            align: 'center',
            customRender: function (t, r, index) {
              return parseInt(index) + 1
            }
          },
          {
            title: '人数',
            align: 'center',
            dataIndex: 'num'
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
            title: '时间',
            align: 'center',
            dataIndex: 'week_dictText'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align: 'center',
            scopedSlots: {customRender: 'action'}
          }
        ],
        url: {
          list: '/scheduling/scheduleSetting/list',
          delete: '/scheduling/scheduleSetting/delete',
          deleteBatch: '/scheduling/scheduleSetting/deleteBatch',
          autSchedule: "/scheduling/scheduleDefine/autSchedule",

        }
      }
    },
    computed: {
      importExcelUrl: function () {
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`
      }
    },
    // created() {
    //   this.getData()
    // },
    methods: {
      //获取班次数据
      getData: function () {
        getAction(this.url.dataList).then(res => {
          if (res.success) {
            this.schedulingName = res.result.records
            console.log(this.schedulingName)
            for (var i = 0; i < this.schedulingName.length; i++) {
              if (this.schedulingName[i].type) {
                if (this.schedulingName[i].type == 'a') {
                  this.schedulingName[i].type = '早班'
                } else if (this.schedulingName[i].type == 'b') {
                  this.schedulingName[i].type = '中班'
                } else if (this.schedulingName[i].type == 'c') {
                  this.schedulingName[i].type = '晚班'
                }
              }
            }
          }
        })
      },
      autSchedule() {
        let that = this;
        postAction(that.url.autSchedule).then((res) => {
          if (res.success) {
            that.$message.success(res.message);
          } else {
            that.$message.warning(res.message);
          }
        })
      }
    }
  }
</script>
<style scoped>
</style>