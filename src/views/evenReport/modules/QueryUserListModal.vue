<!--根据部门IDS筛选用户-->
<template>
  <a-card :bordered="false" v-show="isShow==1">
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">

          <a-col :span="6">
            <a-form-item label="账号">
              <a-input placeholder="请输入账号查询" v-model="queryParam.username"></a-input>
            </a-form-item>
          </a-col>

          <!-- <a-col :span="6">
            <a-form-item label="性别">
              <a-select v-model="queryParam.sex" placeholder="请选择性别查询">
                <a-select-option value="">请选择性别查询</a-select-option>
                <a-select-option value="1">男性</a-select-option>
                <a-select-option value="2">女性</a-select-option>
              </a-select>
            </a-form-item>
          </a-col> -->



            <a-col :span="6">
              <a-form-item label="状态">
                <a-select v-model="queryParam.status" placeholder="请选择用户状态查询">
                  <a-select-option value="">请选择用户状态</a-select-option>
                  <a-select-option value="1">正常</a-select-option>
                  <a-select-option value="2">解冻</a-select-option>
                </a-select>
              </a-form-item>
            </a-col>

          <a-col>
              <a-button type="primary" class="leftBtn" @click="searchByquery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
          </a-col>

        </a-row>
      </a-form>
    </div>

    <a-table
      ref="table"
      rowKey="id"
      :columns="columns"
      :dataSource="dataSource"
      :pagination="ipagination"
      :customRow="setRow"
      :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange }"
      @change="handleTableChange"
    >

    </a-table>
  </a-card>
</template>

<script>
  import {filterObj} from '@/utils/util';
  import {getUserList} from '@/api/api'
  import {getAction} from '@/api/manage'


  export default {
    name: "QueryUserListModal",
    data() {
      return {
        title: "选择用户",
        queryParam: {},
        departIds: [],
        isShow: 0,
        columns: [{
          title: '用户账号',
          align: "center",
          dataIndex: 'username',
          fixed: 'left',
        }, {
          title: '真实姓名',
          align: "center",
          dataIndex: 'realname',
        }, {
          title: '性别',
          align: "center",
          dataIndex: 'sex',
          customRender: function (text) {
            if (text == 1) {
              return "男";
            } else if (text == 2) {
              return "女";
            } else {
              return text;
            }
          }
        }, {
          title: '手机号码',
          align: "center",
          dataIndex: 'phone'
        }, {
          title: '邮箱',
          align: "center",
          dataIndex: 'email'
        }, {
          title: '状态',
          align: "center",
          dataIndex: 'status',
          customRender: function (text) {
            if (text == 1) {
              return "正常";
            } else if (text == 2) {
              return "冻结";
            } else {
              return text;
            }
          }
        }],
        url: {
          list: "/sys/user/queryByDeptIds",
        },
        dataSource: [],
        ipagination: {
          current: 1,
          pageSize: 5,
          pageSizeOptions: ['5', '10', '20'],
          showTotal: (total, range) => {
            return range[0] + "-" + range[1] + " 共" + total + "条"
          },
          showQuickJumper: true,
          showSizeChanger: true,
          total: 0
        },
        isorter: {
          column: 'createTime',
          order: 'desc',
        },
        selectedRowKeys: [],
        selectionRows: [],
        visible: false,
        toggleSearchStatus: false,
      }

    },
    created() {
      this.loadData();
    },
    methods: {
      loadData(arg) {
        if (arg === 1) {
          this.ipagination.current = 1;
        }
        let params = this.getQueryParams();//查询条件
        params.departId=this.departIds;
        getAction(this.url.list, params).then((res) => {
          if (res.success) {
            this.dataSource = res.result.records;
            this.ipagination.total = res.result.total;
          }
        })
      },
      open(record) {
        this.departIds = record.key;
        this.isShow = 1;
        this.loadData(1);
      },


      getQueryParams(record) {
        let param = Object.assign({}, this.queryParam, this.isorter);
        param.field = this.getQueryField();
        param.current = this.ipagination.current;
        param.pageSize = this.ipagination.pageSize;
        return filterObj(param);
      },
      getQueryField() {
        let str = "id,";
        for (let a = 0; a < this.columns.length; a++) {
          str += "," + this.columns[a].dataIndex;
        }
        return str;
      },
      onSelectChange(selectedRowKeys, selectionRows) {
        this.selectedRowKeys = selectedRowKeys;
        this.selectionRows = selectionRows;
        this.$emit("choseUser", this.selectionRows);
      },
      //点击行方法
      setRow(record){
        return {
          on:{
            click:()=>{
              let flag = false;
              let keys = [];
              let currentRecord = [];
              keys = this.selectedRowKeys;
              currentRecord = this.selectionRows;
              if(keys.indexOf(record.id)>=0){
                flag = true
              }
              if(flag){
                keys.splice(keys.findIndex(item=>item===record.id),1);
                this.selectionRows.splice(this.selectionRows.findIndex(item=>item===record.id),1);
                this.$emit("choseUser", this.selectionRows);
              }else{
                keys.push(record.id);
                currentRecord.push(record);
                this.selectionRows = currentRecord;
                this.$emit("choseUser", this.selectionRows);
              }
            }
          }
        }
      },
      handleTableChange(pagination, filters, sorter) {
        //TODO 筛选
        if (Object.keys(sorter).length > 0) {
          this.isorter.column = sorter.field;
          this.isorter.order = "ascend" == sorter.order ? "asc" : "desc"
        }
        this.ipagination = pagination;
        this.loadData();
      },
      handleCancel() {
        this.selectionRows = [];
        this.selectedRowKeys = [];
        this.visible = false;
      },
      searchByquery() {
        this.loadData(1);
      },
      searchReset() {
        let that = this;
        Object.keys(that.queryParam).forEach(function (key) {
          that.queryParam[key] = '';
        });
        that.loadData(1);
      },
      handleToggleSearch() {
        this.toggleSearchStatus = !this.toggleSearchStatus;
      },
    }
  }
</script>
<style scoped>
  .ant-card-body .table-operator {
    margin-bottom: 18px;
  }

  .ant-table-tbody .ant-table-row td {
    padding-top: 15px;
    padding-bottom: 15px;
  }

  .anty-row-operator button {
    margin: 0 5px
  }

  .ant-btn-danger {
    background-color: #ffffff
  }

  .ant-modal-cust-warp {
    height: 100%
  }

  .ant-modal-cust-warp .ant-modal-body {
    height: calc(100% - 110px) !important;
    overflow-y: auto
  }

  .ant-modal-cust-warp .ant-modal-content {
    height: 90% !important;
    overflow-y: hidden
  }

  .anty-img-wrap {
    height: 25px;
    position: relative;
  }

  .anty-img-wrap > img {
    max-height: 100%;
  }
  .leftBtn{
    margin-left: 150px;
  }
</style>