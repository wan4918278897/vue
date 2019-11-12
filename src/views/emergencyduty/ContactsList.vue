<template>
  <a-card :bordered="false" v-show="isShow==1">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">
          <a-col :md="6" :sm="8">
            <a-form-item label="姓名">
              <a-input placeholder="请输入姓名" v-model="queryParam.name"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="移动电话">
              <a-input placeholder="请输入移动电话" v-model="queryParam.mobile"></a-input>
            </a-form-item>
          </a-col>
          <a-col :md="6" :sm="8">
            <a-form-item label="办公电话">
              <a-input placeholder="请输入办公电话" v-model="queryParam.officePhone"></a-input>
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
      <a-button type="primary" icon="download" @click="handleExportXls('通讯录管理')">导出</a-button>
      <a-upload
        name="file"
        :showUploadList="false"
        :multiple="false"
        :headers="tokenHeader"
        :action="importExcelUrl"
        @change="handleImportExcel"
      >
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel">
            <a-icon type="delete" />删除
          </a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px">
          批量操作
          <a-icon type="down" />
        </a-button>
      </a-dropdown>
    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择
        <a style="font-weight: 600">
          {{
          selectedRowKeys.length }}
        </a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>
      <div class="userInformationTable">
        <a-table
          ref="table"
          size="middle"
          bordered
          rowKey="id"
          :columns="columns"
          :dataSource="dataSource"
          :customRow="setRow"
          :pagination="ipagination"
          :loading="loading"
          :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
          @change="handleTableChange"
        >
          <!-- 办公电话 -->
          <span slot="officePhone" slot-scope="text,record">
            {{record.officePhone}}
            <a href="javascript:;" class="ant-dropdown-link">
              <a-icon type="phone" @click="callPhone(record.officePhone)" />
            </a>
          </span>
          <!-- 移动电话 -->
          <span slot="mobile" slot-scope="text,record">
            {{record.mobile}}
            <a href="javascript:;" class="ant-dropdown-link">
              <a-icon type="phone" @click="callPhone(record.mobile)" />
            </a>
          </span>
          <!-- 职务 -->
          <span slot="job" slot-scope="text, record">
            <span :title="record.job">{{record.job}}</span>
          </span>
          <!-- 所属单位 -->
          <span slot="orgName" slot-scope="text, record">
            <span :title="record.orgName">{{record.orgName}}</span>
          </span>
          <!-- 所属部门 -->
          <span slot="departName" slot-scope="text, record">
            <span :title="record.departName ? record.departName : ''">{{record.departName}}</span>
          </span>
          <!-- 值班电话 -->
          <span slot="dutyPhone" slot-scope="text,record">
            {{record.dutyPhone}}
            <a href="javascript:;" class="ant-dropdown-link">
              <a-icon type="phone" @click="callPhone(record.dutyPhone)" />
            </a>
          </span>

          <span slot="action" slot-scope="text, record">
            <a @click="handleEdit(record)">编辑</a>
            <a-divider type="vertical" />
            <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
              <a>删除</a>
            </a-popconfirm>
          </span>
        </a-table>
      </div>
    </div>
    <!-- table区域-end -->

    <!-- 表单区域 -->
    <Contacts-modal ref="modalForm" @ok="modalFormOk" :depart="depart"></Contacts-modal>
  </a-card>
</template>

<script>
import ContactsModal from './modules/ContactsModal'
import { JeecgListMixin } from '@/mixins/JeecgListMixin'
import { getAction, postAction, deleteAction } from '@/api/manage'

export default {
  name: 'ContactsList',
  mixins: [JeecgListMixin],
  components: {
    ContactsModal
  },
  data() {
    return {
      description: '通讯录管理管理页面',
      // 表头
      depart: {
        departId: '',
        orgId: '',
        departName: '',
        orgName: ''
      },
      isShow: 0,
      visible: false,
      columns: [
        {
          title: '序号',
          dataIndex: '',
          key: 'rowIndex',
          width: 50,
          align: 'center',
          customRender: function(t, r, index) {
            return parseInt(index) + 1
          }
        },
        {
          title: '姓名',
          align: 'center',
          dataIndex: 'name'
        },
        {
          title: '移动电话',
          align: 'center',
          dataIndex: 'mobile',
          scopedSlots: { customRender: 'mobile' },
          class: 'specialStyle'
        },
        {
          title: '办公电话',
          align: 'center',
          dataIndex: 'officePhone',
          scopedSlots: { customRender: 'officePhone' },
          class: 'specialStyle'
        },
        {
          title: '职务',
          align: 'center',
          dataIndex: 'job',
          scopedSlots: { customRender: 'job' }
        },
        {
          title: '操作',
          dataIndex: 'action',
          align: 'center',
          scopedSlots: { customRender: 'action' },
          class: 'operateStyle'
        }
      ],
      url: {
        list: '/emergencyduty/contacts/list',
        delete: '/emergencyduty/contacts/delete',
        deleteBatch: '/emergencyduty/contacts/deleteBatch',
        exportXlsUrl: 'emergencyduty/contacts/exportXls',
        importExcelUrl: 'emergencyduty/contacts/importExcel',
        callPhone: window._CONFIG['callPhoneURL'] +'/PhoneCall='
      }
    }
  },
  computed: {
    importExcelUrl: function() {
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`
    }
  },
  created() {},
  methods: {
    loadData() {
      if (!this.url.list) {
        this.$message.error('请设置url.list属性!')
        return
      }
      //加载数据 若传入参数1则加载第一页的内容
      var params = this.getQueryParams() //查询条件
      if (this.currentDeptId != '') {
        params.departId = this.currentDeptId
      }
      //typeof
      if (typeof params.departId == 'undefined') {
        this.isShow = 0
      } else {
        this.isShow = 1
      }
      params.orgId = this.orgId
      //undefined
      getAction(this.url.list, params).then(res => {
        if (res.success) {
          this.dataSource = res.result.records
          this.ipagination.total = res.result.total
          params.orgName = ''
        }
      })
    },
    batchDel: function() {
      if (!this.url.deleteBatch) {
        this.$message.error('请设置url.deleteBatch属性!')
        return
      }
      if (this.selectedRowKeys.length <= 0) {
        this.$message.warning('请选择一条记录！')
        return
      } else {
        var ids = ''
        for (var a = 0; a < this.selectedRowKeys.length; a++) {
          ids += this.selectedRowKeys[a] + ','
        }
        var that = this
        this.$confirm({
          title: '确认删除',
          content: '是否删除选中数据?',
          onOk: function() {
            deleteAction(that.url.deleteBatch, { depId: that.currentDeptId, userIds: ids }).then(res => {
              if (res.success) {
                that.$message.success(res.message)
                that.loadData()
                that.onClearSelected()
              } else {
                that.$message.warning(res.message)
              }
            })
          }
        })
      }
    },
    handleDelete: function(id) {
      if (!this.url.delete) {
        this.$message.error('请设置url.delete属性!')
        return
      }
      var that = this
      deleteAction(that.url.delete, { userId: id }).then(res => {
        if (res.success) {
          that.$message.success(res.message)
          that.loadData()
        } else {
          that.$message.warning(res.message)
        }
      })
    },
    callPhone(record) {
      let hurl = this.url.callPhone + record + '?'
      postAction(hurl).then(res => {

      })
    },
    open(record) {
      this.currentDeptId = record.id
      this.depart.orgId = record.parentId
      this.depart.departId = record.id
      this.depart.departName = record.departName

      this.loadData(1)
    },
    clearList() {
      this.currentDeptId = ''
      this.dataSource = []
    },
    hasSelectDept() {
      if (this.currentDeptId == null) {
        this.$message.error('请选择一个部门!')
        return false
      }
      return true
    },
    handleAddUserDepart() {
      if (this.currentDeptId == '') {
        this.$message.error('请选择一个部门!')
      } else {
        this.$refs.selectUserModal.visible = true
      }
    },
    handleAdd: function() {
      if (this.currentDeptId == '') {
        this.$message.error('请选择一个部门!')
      } else {
        this.$refs.modalForm.departDisabled = true
        this.$refs.modalForm.deptId = this.currentDeptId
        this.$refs.modalForm.add()
        this.$refs.modalForm.title = '新增'
      }
    },
    selectOK(data) {
      let params = {}
      params.depId = this.currentDeptId
      params.userIdList = []
      for (var a = 0; a < data.length; a++) {
        params.userIdList.push(data[a])
      }
      postAction(this.url.edit, params).then(res => {
        if (res.success) {
          this.$message.success(res.message)
          this.loadData()
        } else {
          this.$message.warning(res.message)
        }
      })
    }
  }
}
</script>
<style lang="less">
/** Button按钮间距 */
.ant-btn {
  margin-left: 3px;
}
.table-page-search-wrapper {
  margin-top: -16px;
  margin-bottom: 16px;
}
:global {
  .userInformationTable {
    table {
      table-layout: fixed !important;
    }
    .ant-table-wrapper tr > td:first-of-type {
      // width: 10%;
    }
    .ant-table-wrapper tr > td:not(:first-of-type) {
      width: 20%;
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
       text-align: center;
    }
    .specialStyle {
      width: 120px;
      min-width: 110px;
    }
    .operateStyle {
      width: 120px;
      min-width: 110px;
    }
  }
}
</style>