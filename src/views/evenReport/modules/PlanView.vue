<!--选择部门弹窗-->
<template>
  <a-modal
    :width="modalWidth"
    :visible="visible"
    :maskClosable="false"
    title="预案搜索"
    @ok="handleSubmit"
    @cancel="handleCancel"
    cancelText="关闭"
    >
    <!--wrapClassName="ant-modal-cust-warp"-->
      <!-- <a-col :md="40" :sm="40"> -->
        <a-card :bordered="false">
          <a-row :gutter="10">
            <a-col :md="5" :sm="10" :offset="1">
              <a-select
                @change="onSelect"
                style="width: 120px"
                placeholder="选择分类">
                <a-select-option value="0">总体预案</a-select-option>
                <a-select-option value="1">专项预案</a-select-option>
                <a-select-option value="2">部门预案</a-select-option>
                <a-select-option value="3">重大活动预案</a-select-option>
                <a-select-option value="4">镇街预案</a-select-option>
                <a-select-option value="5">待修预案</a-select-option>
                <a-select-option value="6">其他预案</a-select-option>
              </a-select>
            </a-col>
              <a-col :md="15" :sm="10">
            <a-input-search @search="onSearch" style="width:100%;" placeholder="请输入预案名称"/>
              </a-col>
          </a-row>
          <div style="background: #fff;padding-left:12px;height: 100%; margin-top: 5px">
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
              >
            </a-table>
          </div>
        </a-card>
      <!-- </a-col> -->


  </a-modal>
</template>

<script>
  import pick from 'lodash.pick'
  import {getAction} from '@/api/manage'
  import {queryIdTree} from '@/api/api'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  export default {
    name: "PlanView",
    components: {
    },
    mixins:[JeecgListMixin],
    data() {
      return {

        /* table选中records*/
        userId: "", // 存储用户id字符串
        modalWidth: 700,
        planName:'测试',
        title: "操作",
        visible: false,
        typeCode:'',
        labelCol: {
          xs: {span: 24},
          sm: {span: 5}
        },
        wrapperCol: {
          xs: {span: 24},
          sm: {span: 16}
        },
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
            title: '预案分类',
            align:"center",
            dataIndex: 'typeCode',
            customRender:function (text) {
              if(text=='0'){
                return "总体预案";
              }else if(text=="1"){
                return "专项预案";
              }else if(text=="2"){
                return "部门预案";
              } else if(text="3"){
                return "重大活动预案";
              }else if(text="4"){
                return "镇街预案";
              }else if(text="5"){
                return "待修订预案";
              }else if(text="6"){
                return "其他预案";
              }
            }
          },
          {
            title: '预案名称',
            align:"center",
            dataIndex: 'planName'
          },
          ],
        url: {
          list: "/EventProcessing/reservePlan/list",
        }

      }
    },
    methods: {
      close() {
        this.visible = false;
      },
      show(){
        this.visible=true;
      },
      handleSubmit()
      {
        const that = this;
        // 触发表单验证
        let formData = {
          planId: this.selectedRowKeys[0],
          planName: this.selectionRows[0].planName,
        };
        this.$emit("ok", formData);

        that.close();
      },
      handleCancel()
      {

        this.visible=false;
      },
      //按类型查
      onSelect(value)
      {
        this.queryParam = {};
        //审核状态为通过审核
        this.queryParam.reviewStatus='1';
        this.queryParam.typeCode=value;
        //是否为上报人
        this.queryParam.isReporter='0';
        this.loadData();
      },
      handleTableChange(pagination, filters, sorter) {
        //分页、排序、筛选变化时触发
        //TODO 筛选
        if (Object.keys(sorter).length > 0) {
          this.isorter.column = sorter.field;
          this.isorter.order = "ascend" == sorter.order ? "asc" : "desc"
        }
        this.ipagination = pagination;
        this.loadData();
      },
      //按名称查
      onSearch(value)
      {
        this.queryParam = {};
        //审核状态为通过审核
        this.queryParam.reviewStatus='1';
        this.queryParam.planName=value;
        //是否为上报人
        this.queryParam.isReporter='0';
        this.loadData();
      },

    },
  }
</script>

<style  scoped>
/*
  .ant-table-tbody .ant-table-row td {
    padding-top: 10px;
    padding-bottom: 10px;
  }
  .ant-modal-cust-warp .ant-modal-body{
    height:662px!important;
    background: #ccc;
  }
*/
.ant-form-item {
  margin-bottom: 0;
}


</style>