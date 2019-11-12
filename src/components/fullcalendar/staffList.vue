 <!-- 员工列表 -->
<!--根据部门IDS筛选用户-->
<template>
  <div class="selectModal">
    <a-modal
      :title="title"
      :width="1000"
      :visible="visible"
      @ok="handleOk"
      @cancel="handleCancel"
      cancelText="关闭"
    >
      <a-card :bordered="false">
        <div class="table-page-search-wrapper">
          <a-form layout="inline">
            <a-row :gutter="24">
              <a-col :span="6">
                <a-form-item label="姓名">
                  <a-input placeholder="请输入账号查询" v-model="queryParam.queryValue"></a-input>
                </a-form-item>
              </a-col>
              <a-col>
                <a-button type="primary" class="leftBtn" @click="searchByquery" icon="search">查询</a-button>
                <a-button
                  type="primary"
                  @click="searchReset"
                  icon="reload"
                  style="margin-left: 8px"
                >重置</a-button>
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
        ></a-table>
      </a-card>
    </a-modal>
  </div>
</template>

<script>
import { filterObj } from '@/utils/util'
import { getUserList } from '@/api/api'
import { getAction } from '@/api/manage'
import { JeecgListMixin } from '@/mixins/JeecgListMixin'


export default {
  mixins: [JeecgListMixin],
  name: 'staffList',
  data() {
    return {
      title: '选择员工',
      queryParam: {},
      departIds: [],
      columns: [
        {
          title: '姓名',
          align: 'center',
          dataIndex: 'name'
        },
        {
          title: '性别',
          align: 'center',
          dataIndex: 'sex',
          customRender: function(text) {
            if (text == 1) {
              return '男'
            } else if (text == 2) {
              return '女'
            } else {
              return text
            }
          }
        },
        {
          title: '手机号码',
          align: 'center',
          dataIndex: 'phone'
        },
        {
          title: '部门',
          align: 'center',
          dataIndex: 'deptName'
        }
      ],
      url: {
        list: '/scheduling/scheduleStaff/list'
      },
      dataSource: [],
      ipagination: {
        current: 1,
        pageSize: 5,
        pageSizeOptions: ['5', '10', '20'],
        showTotal: (total, range) => {
          return range[0] + '-' + range[1] + ' 共' + total + '条'
        },
        showQuickJumper: true,
        showSizeChanger: true,
        total: 0
      },
      isorter: {
        column: 'createTime',
        order: 'desc'
      },
      selectedRowKeys: [],
      selectionRows: [],
      visible: false,
      toggleSearchStatus: false
    }
  },
  methods: {
    add: function() {
      this.visible = true
    },
    open(record) {
      this.visible = true
    },

    onSelectChange(selectedRowKeys, selectionRows) {
      this.selectedRowKeys = selectedRowKeys
      this.selectionRows = selectionRows
      this.$emit('choseUser', this.selectionRows)
    },
    //点击行方法
    handleTableChange(pagination, filters, sorter) {
      //TODO 筛选
      if (Object.keys(sorter).length > 0) {
        this.isorter.column = sorter.field
        this.isorter.order = 'ascend' == sorter.order ? 'asc' : 'desc'
      }
      this.ipagination = pagination
      this.loadData()
    },
    handleOk: function() {
       this.selectionRows = []
      this.selectedRowKeys = []
      this.visible = false
    },
    handleCancel() {
      this.selectionRows = []
      this.selectedRowKeys = []
      this.visible = false
    },
    searchByquery() {
      this.loadData(1)
    },
    searchReset() {
      let that = this
      Object.keys(that.queryParam).forEach(function(key) {
        that.queryParam[key] = ''
      })
      that.loadData(1)
    },
    handleToggleSearch() {
      this.toggleSearchStatus = !this.toggleSearchStatus
    }
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
  margin: 0 5px;
}

.ant-btn-danger {
  background-color: #ffffff;
}

.ant-modal-cust-warp {
  height: 100%;
}

.ant-modal-cust-warp .ant-modal-body {
  height: calc(100% - 110px) !important;
  overflow-y: auto;
}

.ant-modal-cust-warp .ant-modal-content {
  height: 90% !important;
  overflow-y: hidden;
}

.anty-img-wrap {
  height: 25px;
  position: relative;
}

.anty-img-wrap > img {
  max-height: 100%;
}
.leftBtn {
  margin-left: 150px;
}
</style>


