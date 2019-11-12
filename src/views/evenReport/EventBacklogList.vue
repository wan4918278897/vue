<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="标题名称">
              <a-input placeholder="请输入标题名称" v-model="queryParam.title"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="发布状态">
              <a-select v-model="queryParam.evenStatus" placeholder="请选择事件状态">
                <!--<a-select-option value="">请选择发布状态</a-select-option>-->
                <a-select-option value="0">未上报</a-select-option>
                <a-select-option value="1">已上报</a-select-option>
                <a-select-option value="2">未处理</a-select-option>
                <a-select-option value="3">未审核</a-select-option>
                <a-select-option value="4">已发布</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>

          <template v-if="toggleSearchStatus">
            <a-col :md="9" :sm="8">
              <a-form-item label="上报日期">
                <a-range-picker showTime v-model="queryTime" >
                </a-range-picker>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="事件级别">
                <a-select
                  placeholder="请输入事件级别"
                  v-model="queryParam.evenLevel"
                >
                  <a-select-option v-for="(item, key) in evenLevel"
                                   :key="key"
                                   :value="item.id">{{item.eventName}}
                  </a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="事件类型">
                <a-select
                  placeholder="请输入事件类型"
                  v-model="queryParam.evenType"
                >
                  <a-select-option v-for="(item, key) in evenType"
                                   :key="key"
                                   :value="item.id">{{item.eventName}}
                  </a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
            <a-col :md="6" :sm="8">
              <a-form-item label="事件性质">
                <a-select
                  placeholder="请输入事件性质"
                  v-model="queryParam.evenProperty"
                >
                  <a-select-option v-for="(item, key) in evenProperty"
                                   :key="key"
                                   :value="item.id">{{item.eventName}}
                  </a-select-option>
                </a-select>
              </a-form-item>
            </a-col>
          </template>
          <a-col :md="6" :sm="8">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="evenQuery" icon="search">查询</a-button>
              <a-button
                type="primary"
                @click="evenReset"
                icon="reload"
                style="margin-left: 8px"
              >重置</a-button>
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
          <a @click="dealInformation(record)" v-if="record.evenStatus==2">处理</a>

          <a href="javascript:;" @click="handleDetail(record)" v-if="record.evenStatus==1">详情</a>
          <a-divider type="vertical"/>
          <a @click="dealWith(record)" v-if="record.evenStatus==1">分派受理</a>


        </span>

      </a-table>
    </div>
    <!-- table区域-end -->
    <eventDealModal ref="eventDealModal" @ok="modalFormOk"></eventDealModal>


    <!-- 表单区域 -->
    <departView ref="departView" @ok="modalForm"></departView>
  </a-card>
</template>

<script>
  import departView from './modules/DepartView'
  import {JeecgListMixin} from '@/mixins/JeecgListMixin'
  import {getAction} from '@/api/manage'
  import eventDealModal from './modules/EventDealModal'
  import moment from 'moment'


  export default {
    name: "EventReportList",
    mixins: [JeecgListMixin],
    components: {
      departView,
      eventDealModal
    },
    data() {
      return {
        userId: "", // 存储用户id字符串
        reportId: "", // 上报事件id
        description: '事件上报管理页面',
        queryTime: [],
        evenType: [],
        evenLevel: [],
        evenProperty: [],
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
            title: '事件标题',
            align: 'center',
            dataIndex: 'title'
          },

          {
            title: '事发时间',
            align: 'center',
            dataIndex: 'date',
            customRender (text) {
              return moment(text).format('YYYY-MM-DD')
            }
          },
          {
            title: '事件性质',
            align: 'center',
            dataIndex: 'property'
          },
          {
            title: '事件类型',
            align: 'center',
            dataIndex: 'type'
          },
          {
            title: '事件级别',
            align: 'center',
            dataIndex: 'level'
          },
          {
            title: '事件状态',
            align: 'center',
            dataIndex: 'evenStatus',
            customRender (text) {
              if(text=='0'){
                return "未上报";
              }else if(text=="1"){
                return "已上报";
              }else if(text=="2"){
                return "未处理";
              }else if(text=="3"){
                return "未审核";
              }else if(text=="4"){
                return "已发布";
              }
            }
          },
          {
            title: '操作',
            dataIndex: 'action',
            align: 'center',
            scopedSlots: {customRender: 'action'}
          }
        ],

        url: {
          list: "/even/eventBacklog/list",
          dealWith: "/even/eventBacklog/dealWith",
          delete: "/even/eventBacklog/delete",
          queryEventSetting: '/even/eventReport/selectValue'

        },
      }
    },
    created() {
      this.loadData();
      this.getEventType();
    },
    methods: {
      dealWith(record) {
        this.$refs.departView.add();
        this.reportId = record.id;
      },
      modalForm(userList) {
        this.selectedUser = userList.selectedUser;
        this.userId = userList.userId;
        let params = {id: this.reportId, userId: this.userId}
        getAction(this.url.dealWith, params).then((res) => {
          if (res.success) {
            this.loadData();
          }
        })
      },
      evenQuery(){
        if (this.queryTime.length==2){
          this.queryParam.beginDate=moment(this.queryTime[0]).format('YYYY-MM-DD');
          this.queryParam.endDate=moment(this.queryTime[1]).format('YYYY-MM-DD');
        }
        this.searchQuery()
      },
      evenReset(){
        this.queryTime=[];
        this.searchReset();
      },
      getEventType() {
        getAction(this.url.queryEventSetting).then((res) => {
          for (var i = 0; i < res.length; i++) {
            if (res[i].eventType == 'A') {
              this.evenLevel.push(res[i]);
            } else if (res[i].eventType == 'B') {
              this.evenType.push(res[i]);
            } else if (res[i].eventType == 'C') {
              this.evenProperty.push(res[i]);
            }
          }

        })
      },

      dealInformation(record){
        this.$refs.eventDealModal.open(record,'1');
      },
      handleDetail(record){
        this.$refs.eventDealModal.open(record);
        this.$refs.eventDealModal.title="详情";
        this.$refs.eventDealModal.disableSubmit = true;
        this.$refs.eventDealModal.okButtonProps= true;
      }
    }
  }
</script>
<style scoped>
</style>