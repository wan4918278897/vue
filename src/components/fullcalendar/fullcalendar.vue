<template>
  <!-- 日历插件模态框 -->
  <div class="wrapper">
    <full-calendar :config="config" :events="events" @event-selected="eventClick"></full-calendar>
    <div class="selectModal">
      <a-modal
        :title="title"
        :width="800"
        :visible="visible"
        :confirmLoading="confirmLoading"
        @cancel="handleCancel"
        cancelText="关闭"
      >
        <template slot="footer">
          <a-button type="primary" @click="handleOk">确定</a-button>
          <a-button type="danger" @click="showDeleteConfirm">删除</a-button>
          <a-button @click="handleCancel">关闭</a-button>
        </template>
        <!-- :footer="footer" -->
        <a-spin :spinning="confirmLoading">
          <a-form :form="form">
            <a-row :gutter="10" class="lastRow">
              <a-col :md="10" :sm="10" :offset="2">
                <a-form-item :labelCol="newLabelCol" :wrapperCol="newWrapperCol" label="班次">
                  <a-select
                    defaultValue="早班"
                    @change="handleChange"
                    v-model="scheduleType"
                    :disabled="isShow"
                  >
                    <a-select-option
                      :value="item.id"
                      v-for="(item,index) in scheduleTypeList"
                      :key="index"
                    >{{item.scheduleType_dictText}}</a-select-option>
                  </a-select>
                </a-form-item>
              </a-col>
              <a-col :md="10" :sm="10">
                <a-form-item :labelCol="newLabelCol" :wrapperCol="newWrapperCol" label="姓名">
                  <a-select
                    mode="multiple"
                    style="width: 100%"
                    placeholder="请选择员工"
                    v-model="userName"
                    @dropdownVisibleChange="onSearch"
                  ></a-select>
                </a-form-item>
              </a-col>
            </a-row>
            <a-row :gutter="10">
              <a-col :md="20" :sm="20" :offset="2">
                <a-form-item :labelCol="LabelCol" :wrapperCol="WrapperCol" label="描述">
                  <a-textarea
                    class="marginLeft"
                    :rows="3"
                    placeholder="备注"
                    v-model="describe"
                    :disabled="true"
                  />
                </a-form-item>
              </a-col>
            </a-row>
          </a-form>
        </a-spin>
        <!-- 员工列表 -->
        <staffList ref="staffList" @choseUser="choseUser"></staffList>
      </a-modal>
    </div>
  </div>
</template>
<script>
import { JeecgListMixin } from '@/mixins/JeecgListMixin'
import staffList from './staffList'
import { FullCalendar } from 'vue-full-calendar'
import { getAction, httpAction, deleteAction } from '@/api/manage'

export default {
  name: 'fullcalendar',
  components: {
    staffList,
    FullCalendar
  },
  data() {
    return {
      visible: false,
      confirmLoading: false,
      title: '选择班次',
      userDepartModel: { userId: '', userName: [] },
      userName: [],
      userIds: [],
      describe: '',
      scheduleType: '',
      isShow: false, //false  不能编辑
      scheduleTypeList: [],
      id: '',
      newLabelCol: {
        xs: { span: 5 },
        sm: { span: 5 }
      },
      newWrapperCol: {
        xs: { span: 18 },
        sm: { span: 18 }
      },
      LabelCol: {
        xs: { span: 3 },
        sm: { span: 2 }
      },
      WrapperCol: {
        xs: { span: 17 },
        sm: { span: 21 }
      },

      form: this.$form.createForm(this),
      events: [],
      config: {
        than: this,
        defaultView: 'agendaWeek', //默认显示周
        locale: 'zh-cn', //中文
        firstDay: '1',
        header: {
          left: 'prev,next today', //上一页、下一页、今天
          center: 'title myCustomButton', //居中：时间范围区间标题
          right: 'agendaWeek,month,agendaDay,listWeek' //右边：显示哪些视图
        },
        buttonText: { today: '今天', month: '月', week: '周', day: '日', listWeek: '列表' },
        // selectable: true, //允许用户可以长按鼠标选择多个区域(比如月视图，可以选中多天；日视图可以选中多个小时)，默认false不能选择多区域的
        selectMirror: true,
        editable: false,
        navLinks: true,
        eventLimitClick: 'day',
        firstHour: '08:00',
        // allDayDefault: false,
        // defaultView: 'agendaDay',
        slotLabelFormat:'HH:mm',   //时间24小时制
        // axisFormat: 'HH:mm',
      },
      url: {
        list: '/scheduling/scheduleDuty/list',
        add: '/scheduling/scheduleDuty/add',
        edit: '/scheduling/scheduleDuty/edit',
        detail: '/scheduling/scheduleDuty/getDutyDetail',
        selectDefineList: '/scheduling/scheduleDefine/list',
        delete: '/scheduling/scheduleDuty/delete'
      },
      colorList: ['#A9D08E', '#B4C6E7', '#F4B084', '#4472C4'],
      colorLength: 3
    }
  },
  created: function() {
    this.loadData()
    this.getSelectValue()
  },
  methods: {
    // dayClick(date, jsEvent, view) {
    //   console.log("2222222")
    //
    //   this.visible = true
    // },
    select: function(start, end, jsEvent, view) {},
    eventClick(event, jsEvent, pos) {
      this.visible = true
      this.detail(event)
    },
    loadData: function() {
      getAction(this.url.list).then(res => {
        if (res.success) {
          let data = res.result
          for (var i = 0; i < data.length; i++) {
            let s = i % this.colorLength
            if (!data[i].userName) {
              data[i].userName = ''
            }
            let stringValue = String(data[i].userName)
            data[i].userName = stringValue.substring(0, stringValue.length - 1)
            this.events.push({
              id: data[i].id,
              start: data[i].startDate + ' ' + data[i].startTime,
              end: data[i].endDate + ' ' + data[i].endTime,
              color: this.colorList[s],
              title: '值班人员：' + data[i].userName,
              className: 'titleStyle'
            })
          }
        }
      })
    },

    // 确定
    handleOk() {
      const that = this
      let httpurl = ''
      let method = ''
      if (that.id != '') {
        httpurl += this.url.edit
        method += 'put'
      } else {
        httpurl += this.url.add
      }
      let param = {
        id: that.id,
        scheduleId: that.scheduleType,
        userIds: this.userIds
      }
      httpAction(httpurl, param, method)
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
          that.close()
        })
    },
    close() {
      this.$emit('close')
      this.visible = false
      this.selectedUser = []
      this.userIds = ''
    },
    // 取消
    handleCancel() {
      this.visible = false
    },
    // select改变
    handleChange(value) {
      console.log(`selected ${value}`)
    },
    modalFormOk(userList) {
      this.selectedUser = userList.selectedUser
      this.userIds = userList.userId
    },
    // 选择员工
    onSearch() {
      this.$refs.staffList.open()
    },
    getUser(record) {
      this.model = Object.assign({}, record)
      if (record.id != '') {
        getAction(this.url.queryByReportId, { id: record.id }).then(res => {
          if (res.success) {
            for (var i = 0; i < res.result.length; i++) {
              this.selectedUser.push(res.result[i].realname)
            }
            this.$refs.staffList.tipDetail(res.result, this.userIds)
          }
        })
      }
    },
    choseUser: function(userList) {
      this.userName = []
      this.userIds = []
      console.log(userList)
      for (var i = 0; i < userList.length; i++) {
        this.userName.push(userList[i].name)
        this.userIds += userList[i].userId + ','
      }
    },
    // 点击事件展示详情
    detail(record) {
      this.id = record.id
      let param = { id: record.id }
      getAction(this.url.detail, param).then(res => {
        if (res.success) {
          console.log(res)
          let record = res.result
          this.userName = record.userName
          this.userIds = record.userIds
          this.scheduleType = record.scheduleId
          this.describe = record.describe
        }
      })
    },
    //确认删除框
    showDeleteConfirm() {
      let id = this.id
      let url = this.url.delete
      let that = this
      this.$confirm({
        title: '确定要删除这条信息吗？',
        // content: 'Some descriptions',
        okText: '确定',
        okType: 'danger',
        cancelText: '取消',
        onOk() {
          deleteAction(url, { id: id }).then(res => {
            if (res.success) {
              that.visible = false
              that.$message.success(res.message)
            } else {
              that.visible = false
              that.$message.warning(res.message)
            }
          })
        },
        onCancel() {
          console.log('Cancel')
        }
      })
    },
    //获取班次定义下拉框值
    getSelectValue() {
      getAction(this.url.selectDefineList).then(res => {
        if (res.success) {
          this.scheduleTypeList = res.result.records
          console.log(this.scheduleTypeList)
        }
      })
    }
  }
}
</script>
<style>
.marginLeft {
  margin-left: 14px;
}

.titleStyle {
  text-align: center;
  font-size: 16px;
  padding: 12px;
}

.fc-title {
  padding-top: 10px;
}
</style>



