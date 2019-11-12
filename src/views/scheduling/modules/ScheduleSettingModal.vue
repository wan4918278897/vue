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
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="人数">
          <a-input-number
            v-decorator="[ 'num', {rules:[{required: true,message: '请输入人数'}]}]"
            style="width: 60%"
          />
        </a-form-item>
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="班次">
          <a-select initialValue="早班" style="width: 60%" v-model="scheduleId">
            <a-select-option
              v-for="(item,index) in scheduleType"
              :key="index"
              :value="item.id"
            >{{item.scheduleType_dictText}}</a-select-option>
          </a-select>
        </a-form-item>

        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="时间">
          <a-select
            v-model="week"
            mode="multiple"
            style="width: 60%"
            @change="handleChange"
            placeholder="请选择时间"
          >
            <a-select-option
              v-for="(item,index) in timeList"
              :key="index"
              :value="item.value"
            >{{item.key}}</a-select-option>
          </a-select>
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
import { httpAction } from '@/api/manage'
import pick from 'lodash.pick'
import moment from 'moment'
import { getAction } from '@/api/manage'

export default {
  props: ['timeList'],
  name: 'ScheduleSettingModal',
  data() {
    return {
      title: '操作',
      visible: false,
      scheduleType: [],
      scheduleId: '',
      model: {},
      labelCol: {
        xs: { span: 24 },
        sm: { span: 5 }
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 16 }
      },

      scheduleId: '',
      week: [],
      modalTipText: '',
      confirmLoading: false,
      form: this.$form.createForm(this),
      validatorRules: {},
      url: {
        add: '/scheduling/scheduleSetting/add',
        edit: '/scheduling/scheduleSetting/edit',
        dataList: '/scheduling/scheduleDefine/list'
      }
    }
  },

  created() {
    // this.getData()
  },
  methods: {
    // 获取班次数据
    add() {
      getAction(this.url.dataList).then(res => {
        if (res.success) {
          this.scheduleType = res.result.records
        }
      })

      this.edit({})
    },
    edit(record) {
      this.form.resetFields()
      this.model = Object.assign({}, record)
      this.visible = true
      this.$nextTick(() => {
        this.form.setFieldsValue(pick(this.model, 'num', 'week'))
        //时间格式化
      })
    },
    close() {
      this.$emit('close')
      this.visible = false
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
          if (!this.model.id) {
            httpurl += this.url.add
            method = 'post'
          } else {
            httpurl += this.url.edit
            method = 'put'
          }
          let formData = Object.assign(this.model, values)
          formData.scheduleId = this.scheduleId
          formData.week = this.week.join(',')
          if (!formData.week || !formData.scheduleId) {
            that.confirmLoading = false
            that.modalTipText = '该字段为必填项！'
            that.error()
            return
          }
          console.log(formData)
          console.log('xxx')

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
            })
        }
      })
    },
    handleCancel() {
      this.close()
    },
    handleChange(value) {
      console.log(`selected ${value}`)
    }
  }
}
</script>

<style lang="less" scoped>
  .ant-form-item {
    margin-bottom: 0;
  }
</style>