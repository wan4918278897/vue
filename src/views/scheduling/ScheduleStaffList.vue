<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline">
        <a-row :gutter="24">

          <a-col :md="6" :sm="8">
            <a-form-item label="姓名">
              <a-input placeholder="请输入姓名..." v-model="queryValue"></a-input>
            </a-form-item>
          </a-col>

          <a-col :md="6" :sm="8">
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
      <a-button @click="add" type="primary" icon="plus">新增</a-button>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel">
            <a-icon type="delete"/>
            删除
          </a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作
          <a-icon type="down"/>
        </a-button>
      </a-dropdown>
    </div>

    <!-- table区域-begin -->
    <a-table
      ref="table"
      size="middle"
      bordered
      rowKey="id"
      :columns="columns"
      :dataSource="dataSource"
      :pagination="ipagination"
      :customRow="setRow"
      :loading="loading"
      :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
      @change="handleTableChange">

      <template slot="avatarslot" slot-scope="text, record, index">
        <div class="anty-img-wrap">
          <a-avatar shape="square" :src="getAvatarView(record.avatar)" icon="user"/>
        </div>
      </template>


      <span slot="action" slot-scope="text, record">
        <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
        </a-popconfirm>
         <a-divider type="vertical"/>
        <a @click="setRest(record)">设置休息日</a>
        </span>


    </a-table>


    <a-modal
      title="设置休息日期"
      :visible="visible"
      :width="600"
      :maskClosable="false"
      @ok="handleOk"
      @cancel="handleCancel"
      okText="提交"
      cancelText="关闭"
    >
      <a-select
        mode="tags"
        style="width: 100%"
        placeholder="Tags Mode"
        v-model="week"
      >
        <a-select-option
          v-for="(item,index) in weekList"
          :key="index"
          :value="item.value">
          {{item.key}}
        </a-select-option>
      </a-select>
    </a-modal>

    <!-- 表单区域 -->
    <scheduleStaff-modal ref="modalForm" @ok="modalFormOk"></scheduleStaff-modal>

    <departView ref="departView" @ok="selectUser"></departView>

  </a-card>
</template>

<script>
  import ScheduleStaffModal from './modules/ScheduleStaffModal'
  import {JeecgListMixin} from '@/mixins/JeecgListMixin'
  import {getAction, postAction} from '@/api/manage'
  import departView from '../evenReport/modules/DepartView'


  export default {
    name: "ScheduleStaffList",
    mixins: [JeecgListMixin],
    components: {
      ScheduleStaffModal,
      departView
    },
    data() {
      return {
        description: '值班人员管理',
        queryValue: '',//查询值
        selectedUser: '',
        userIds: '',
        setRestId: '',//设置休息时绑定员工ID
        week: [],//选定的日期
        weekList: [
          {value: '1', key: '周一' },
          {value: '2', key: '周二'},
          {value: '3', key: '周三'},
          {value: '4', key: '周四'},
          {value: '5', key: '周五'},
          {value: '6', key: '周六'},
          {value: '7', key: '周日'},
        ],
        //选定的日期
        visible: false,
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
            title: '姓名',
            align: "center",
            dataIndex: 'name'
          },
          {
            title: '性别',
            align: "center",
            dataIndex: 'sex',
            customRender: function (text) {
              if (text === '1') {
                return '男'
              } else if (text === '2') {
                return '女'
              } else {
                return text
              }
            }
          },
          {
            title: '头像',
            align: "center",
            width: 120,
            dataIndex: 'avatar',
            scopedSlots: {customRender: "avatarslot"}
          },
          {
            title: '手机号码',
            align: "center",
            width: 100,
            dataIndex: 'phone'
          },
          {
            title: '部门名称',
            align: "center",
            dataIndex: 'deptName'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align: "center",
            scopedSlots: {customRender: 'action'},
          }
        ],
        url: {
          list: "/scheduling/scheduleStaff/list",
          add: "/scheduling/scheduleStaff/add",
          delete: "/scheduling/scheduleStaff/delete",
          deleteBatch: "/scheduling/scheduleStaff/deleteBatch",
          setRest: "/scheduling/scheduleStaff/setRest"
          // exportXlsUrl: "scheduling/scheduleStaff/exportXls",
          // importExcelUrl: "scheduling/scheduleStaff/importExcel",
        },
      }
    },
    created() {
    this.yzx();
    },
    methods: {
      getAvatarView: function (avatar) {
        return this.url.imgerver + "/" + avatar;
      },
      yzx(recored) {

        console.log("yyyyyyyyyyyyy")
        if (!this.url.list) {
          this.$message.error("请设置url.list属性!")
          return
        }
        this.loading = true;
        let params = this.getQueryParams();//查询条件
        params.queryValue = recored
        getAction(this.url.list, params).then((res) => {
          if (res.success) {
            this.dataSource = res.result.records;
            this.ipagination.total = res.result.total;
            console.log(res.result.records)
          }
          this.loading = false;
        })
      },
      searchQuery() {
        this.loadData(this.queryValue);
      },
      add() {
        this.$refs.departView.add();
      },
      selectUser(userList) {
        this.selectedUser = userList.selectedUser;
        this.userIds = userList.userId;
        let params = {userIds: this.userIds}
        postAction(this.url.add, params).then((res) => {
          if (res.success) {
            this.loadData();
          }
        })
        console.log(userList)
      },
      searchReset() {
        this.queryValue = '';
        this.loadData();
      },
      setRest(record) {
        this.visible = true;
        this.setRestId = record.id;
        this.week=record.restDays.split(',');
        this.week.pop();
        this.week.sort();
        console.log(this.week)
      },
      handleOk() {
        const that = this
        let s = '';
        for (let i = 0; i < this.week.length; i++) {
          s += this.week[i] + ","
        }
        let params = {str: s, id: this.setRestId}
        postAction(this.url.setRest, params)
          .then(res => {
            if (res.success) {
              that.$message.success(res.message)
              that.$emit('ok')
            } else {
              that.$message.warning(res.message)
            }
          })
          .finally(() => {
            that.confirmLoading = false
            console.log('ssssssss')
            this.close()
            this.loadData()
            this.week=[]
          })
      },
      handleCancel() {
        this.close()
      },
      close() {
        this.visible = false;
        this.week = [];
        this.setRestId = '';
      },

    }
  }
</script>
<style scoped>
</style>