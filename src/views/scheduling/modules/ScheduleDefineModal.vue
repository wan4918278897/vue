<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    :maskClosable="false"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭"
  >
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-row :gutter="1">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item :labelCol="{ span: 8 }" :wrapperCol="{ span: 15 }" label="班次名称">
              <a-select
                @change="selectValue"
                v-decorator="['scheduleType', {initialValue:'a',rules:[{required: true,message:'班次名称不能为空'}]}]"
              >
                <a-select-option
                  v-for="(item,index) in schedulingName"
                  :key="index"
                  :value="item.value"
                >{{item.key}}</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item :labelCol="{ span: 9 }" :wrapperCol="{ span: 15 }" label="该班次后不能排">
              <a-select initialValue="a" v-decorator="['refuseSchedule', {}]">
                <a-select-option
                  v-for="(item,index) in schedulingName"
                  :key="index"
                  :value="item.value"
                  :disabled="item.disabled"
                >{{item.key}}</a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
        </a-row>

        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="排班人员">
          <a-select
            mode="multiple"
            style="width: 94%"
            placeholder="请选择用户"
            v-model="selectedUser"
            @dropdownVisibleChange="onSearch"
          ></a-select>
        </a-form-item>
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="优先级">
          <a-input
            placeholder="请输入优先级"
            style="width: 94%"
            v-decorator="['priority', {rules:[{required: true,message:'优先级不能为空'}]}]"
          />
        </a-form-item>
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="班次描述">
          <a-input placeholder="请输入班次描述" style="width: 94%" v-decorator="['scheduleDescribe', {}]" />
        </a-form-item>
        <a-row :gutter="1" style="margin-left:32px !important;">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item :labelCol="{ span: 6 }" :wrapperCol="{ span: 18}" label="开始时间">
              <a-row :gutter="1">
                <a-col :md="10" :sm="10">
                  <a-form-item :labelCol="{ span: 8 }" :wrapperCol="{ span: 16 }" label="时">
                    <a-select defaultValue="00" v-model="startHours">
                      <a-select-option v-for="n in hoursList" :key="n" :value="n">{{n}}</a-select-option>
                    </a-select>
                  </a-form-item>
                </a-col>
                <a-col :md="10" :sm="10">
                  <a-form-item :labelCol="{ span: 8 }" :wrapperCol="{ span: 16 }" label="分">
                    <a-select defaultValue="00" v-model="startMinute">
                      <a-select-option v-for="n in minuteList" :key="n" :value="n">{{n}}</a-select-option>
                    </a-select>
                  </a-form-item>
                </a-col>
              </a-row>
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item :labelCol="{ span: 6 }" :wrapperCol="{ span: 18 }" label="结束时间">
              <a-row :gutter="1">
                <a-col :md="10" :sm="10">
                  <a-form-item :labelCol="{ span: 8 }" :wrapperCol="{ span: 16 }" label="时">
                    <a-select defaultValue="00" v-model="endHours">
                      <a-select-option v-for="n in hoursList" :key="n" :value="n">{{n}}</a-select-option>
                    </a-select>
                  </a-form-item>
                </a-col>
                <a-col :md="10" :sm="10">
                  <a-form-item :labelCol="{ span: 8 }" :wrapperCol="{ span: 16 }" label="分">
                    <a-select defaultValue="00" v-model="endMinute">
                      <a-select-option v-for="n in minuteList" :key="n" :value="n">{{n}}</a-select-option>
                    </a-select>
                  </a-form-item>
                </a-col>
              </a-row>
            </a-form-item>
          </a-col>
        </a-row>
      </a-form>
    </a-spin>
    <!-- 员工列表 -->
    <staffList ref="staffList" @choseUser="choseUser"></staffList>
  </a-modal>
</template>

<script>
import { httpAction } from '@/api/manage'
import pick from 'lodash.pick'
import { getAction } from '@/api/manage'
import staffList from '../../../components/fullcalendar/staffList'

export default {
  name: 'ScheduleDefineModal',
  components: {
    staffList
  },
  data() {
    return {
      modalTipText: '',
      title: '操作',
      visible: false,
      model: {},
      startTime: '',
      endTime: '',
      startHours: '00',
      startMinute: '00',
      endHours: '00',
      endMinute: '00',

      selectedUser: [],
      userIds: [],
      scheduleType: '',
      hoursList: [],
      minuteList: ['00', '10', '20', '30', '40', '50'],
      schedulingName: [
        { value: 'a', key: '早班', disabled: false },
        { value: 'b', key: '中班', disabled: false },
        { value: 'c', key: '晚班', disabled: false }
      ],
      labelCol: {
        xs: { span: 24 },
        sm: { span: 5 }
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 18 }
      },

      confirmLoading: false,
      form: this.$form.createForm(this),
      validatorRules: {},
      url: {
        add: '/scheduling/scheduleDefine/add',
        edit: '/scheduling/scheduleDefine/edit',
        queryDealList: '/scheduling/schedulePerson/findSchedulePerson'
      }
    }
  },
  created() {
    for (var i = 0; i < 24; i++) {
      i = i < 10 ? '0' + i : i
      this.hoursList.push(i)
    }
  },
  methods: {
    add() {
      this.edit({})
      this.startHours = '00'
      this.startMinute = '00'
      this.endHours = '00'
      this.endMinute = '00'
    },
    edit(record) {
      console.log(record)
      this.form.resetFields()
      this.model = Object.assign({}, record)
      this.visible = true
      if (JSON.stringify(record) == '{}') {
        this.startHours = ''
        this.startMinute = ''
        this.endHours = ''
        this.endMinute = ''
      } else {
        this.startHours = record.startTime.substring(0, 2)
        this.startMinute = record.startTime.substring(3, 5)
        this.endHours = record.endTime.substring(0, 2)
        this.endMinute = record.endTime.substring(3, 5)
      }
      this.$nextTick(() => {
        this.form.setFieldsValue(pick(this.model, 'priority', 'scheduleDescribe', 'scheduleType'))
      })
    },
    close() {
      this.$emit('close')
      this.visible = false
      this.selectedUser = []
      this.userIds = []
      this.visible = false
      // this.startHours = ''
      // this.startMinute = ''
      // this.endHours = ''
      // this.endMinute = ''
    },
    error() {
      this.$message.error(this.modalTipText)
    },
    handleOk() {
      const that = this
      // 触发表单验证
      this.form.validateFields((err, values) => {
        if (!err) {
          that.confirmLoading = true
          let httpurl = ''
          let method = ''
          if (this.userIds.length == 0) {
            that.modalTipText = '请选择排班人员'
            that.confirmLoading = false
            that.error()
            return
          }
          httpurl += this.url.add + '?userIds=' + this.userIds
          method = 'post'
          let formData = Object.assign(this.model, values)
          formData.startMinute = this.startMinute
          formData.endMinute = this.endMinute
          formData.startHours = this.startHours
          formData.endHours = this.endHours
          if (formData.scheduleType !=='c' && parseInt(formData.startHours) > parseInt(formData.endHours)) {
            that.modalTipText = '开始时间不能大于结束时间'
            that.confirmLoading = false
            that.error()
            return
          }
          formData.startTime = formData.startHours + ':' + formData.startMinute + ':' + '00'
          formData.endTime = formData.endHours + ':' + formData.endMinute + ':' + '00'
          httpAction(httpurl, formData, method)
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
              // that.loadData()
            })
        }
      })
    },
    handleCancel() {
      this.close()
    },
    // 单选框选中
    onChange(e) {
      console.log('radio checked', e.target.value)
    },
    // 选择员工
    onSearch() {
      this.$refs.staffList.open()
    },
    choseUser: function(userList) {
      this.selectedUser = []
      this.userIds = []
      for (var i = 0; i < userList.length; i++) {
        this.selectedUser.push(userList[i].name)
        this.userIds += userList[i].userId + ','
      }
    },
    // 选中select值 disabled
    selectValue(value) {
      this.scheduleType = value
      let schedulingList = this.schedulingName
      for (var i = 0; i < schedulingList.length; i++) {
        if (value == schedulingList[i].value) {
          schedulingList[i].disabled = true
        } else {
          schedulingList[i].disabled = false
        }
      }
    },
    getData: function() {
      let id = sessionStorage.getItem('id')
      if (id !== '') {
        getAction(this.url.queryDealList, { id: id }).then(res => {
          if (res.success) {
            let data = res.result
            if (typeof data == null) {
              this.selectedUser = []
            } else {
              for (var i = 0; i < data.length; i++) {
                this.selectedUser.push(data[i].realname)
                this.userIds.push(data[i].id)
              }
            }
            console.log(this.selectedUser)
          }
        })
      } else {
        return
      }
    },
    modalFormOk(userList) {
      this.selectedUser = userList.selectedUser
      this.userIds = userList.userId
      this.getData()
    },
    open(record) {
      this.edit(record)
      getAction(this.url.queryDealList, { id: record.id }).then(res => {
        if (res.success) {
          let data = res.result
          if (typeof data == null) {
            this.selectedUser = []
          } else {
            for (var i = 0; i < data.length; i++) {
              this.selectedUser.push(data[i].realname)
              this.userIds.push(data[i].id)
            }
          }
        }
      })
    }
  }
}
</script>
<style>
.ant-col-sm-5 {
  width: 22%;
}
/* 信息提示框 */
.ant-row {
  position: relative;
}
.messageTip {
  color: #f5222d;
  margin-top: -20px;
  position: absolute;
  bottom: 0;
  left: 12%;
}
.ant-form-item {
  margin-bottom: 0;
}
</style>
