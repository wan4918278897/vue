<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper" style="margin-bottom: 10px">
      <a-form layout="inline">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-button  type="primary" @click="week(-1)"><a-icon type="left" /></a-button>
            <a-button  type="primary" style="margin-left: 8px" @click="week(1)"><a-icon type="right" /></a-button>
<!--            <a-input  v-model="this.queryParam.beginDate"></a-input>-->
<!--            <a-input  v-model="this.queryParam.endDate"></a-input>-->
          </a-col>

        </a-row>
      </a-form>
    </div>
    <!-- table区域-begin -->
    <div>
<!--      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">-->
<!--        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择-->
<!--        <a style="font-weight: 600">-->
<!--          {{-->
<!--          selectedRowKeys.length }}-->
<!--        </a>项-->
<!--        <a style="margin-left: 24px" @click="onClearSelected">清空</a>-->
<!--      </div>-->

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
        @change="handleTableChange"
      >
      </a-table>
    </div>
    <!-- table区域-end -->

    <!-- 表单区域 -->
    <eventReport-modal ref="modalForm" @ok="modalFormOk"></eventReport-modal>
  </a-card>
</template>

<script>
  import {JeecgListMixin} from '@/mixins/JeecgListMixin'
  import {getAction} from '@/api/manage'
  import moment from 'moment'
  var base=0;
  export default {
    name: 'EventReportList',
    mixins: [JeecgListMixin],
    data() {
      return {
        description: '处理人',
        userName: [],
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
          }
        ],
        url: {
          list: '/even/eventReport/list'
        }
      }
    },
    created() {
      this.loadData()
    },
    methods: {
      detail(record) {
        this.userName = []
        getAction(this.url.list, {reportId: record.id}).then(res => {
          if (res.success) {
            console.log(res)
            for (var i = 0; i < res.result.length; i++) {
              this.userName.push(res.result[i])
            }
            console.log(this.userName)
          }
        })
      },week(num){
            var date = new Date(),
                nowdata=date.getFullYear()+"-"+(date.getMonth()+1)+"-"+date.getDate();
            base=base+num;
            console.info(base);
            this.queryParam.beginDate=fun_date(30,1,base);
            this.queryParam.endDate=fun_date2(fun_date(30,1,base));
            this.searchQuery()
        },
        beforeDestroy() {
      }
    }
  }
  //以当前时间前后变化，第一个参数为变化天数，第二个参数为向前或向后推迟时间，第三个参数变化天数基数
  function fun_date(num,type,base){
      var date1 = new Date(),
          time1=date1.getFullYear()+"-"+(date1.getMonth()+1)+"-"+date1.getDate();//time1表示当前时间
      var date2 = new Date(date1);
      if(type==1){
          date2.setDate(date1.getDate()+num*base);
      }else{
          date2.setDate(date1.getDate()+num);
      }
      var time2 = date2.getFullYear()+"-"+(date2.getMonth()+1)+"-"+date2.getDate();
     return time2;
  }
  //指定时间加30
  function fun_date2(num){
      var date1 = new Date(num),
          time1=date1.getFullYear()+"-"+(date1.getMonth()+1)+"-"+date1.getDate();//time1表示当前时间
      var date2 = new Date(date1);
      date2.setDate(date1.getDate()+30);
      var time2 = date2.getFullYear()+"-"+(date2.getMonth()+1)+"-"+date2.getDate();
      return time2;
  }


</script>