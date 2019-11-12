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
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="姓名">
          <a-input placeholder="请输入姓名" v-decorator="['name', {}]"/>
        </a-form-item>
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="职务">
          <a-input placeholder="请输入职务" v-decorator="['job', {}]"/>
        </a-form-item>
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="移动电话" help="">
          <a-input placeholder="请输入移动电话" v-decorator="['mobile',{rules: [{ required: true, message: '请输入移动电话'},{validator: validateMobile}]}]"/>
        </a-form-item>
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="办公电话" help="">
          <a-input placeholder="请输入办公电话" v-decorator="['officePhone',{rules: [{ required: true, message: '请输入办公电话'},{validator: validateOfficePhone}]}]"/>
        </a-form-item>
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="值班电话" help="">
          <a-input placeholder="请输入值班电话" v-decorator="['dutyPhone', {rules: [{ required: true, message: '请输入值班电话'},{validator: validateOfficePhone}]}]"/>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="排序">
          <a-input-number :min="0" placeholder="请输入排序号" v-decorator="['sort', {}]"/>
        </a-form-item>
        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="选择部门">
          <a-input placeholder="选择部门" v-model="depart.departName" disabled/>
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
import { httpAction } from '@/api/manage'
import pick from 'lodash.pick'
import JSelectDepart from '@/components/jeecgbiz/JSelectDepart'

export default {
  name: 'ContactsModal',
  components: {
    JSelectDepart
  },
  props: ['depart'],
  data() {
    return {
      title: '操作',
      visible: false,
      model: {},
      labelCol: {
        xs: { span: 24 },
        sm: { span: 5 }
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 16 }
      },

      confirmLoading: false,
      form: this.$form.createForm(this),
      url: {
        add: '/emergencyduty/contacts/add',
        edit: '/emergencyduty/contacts/edit'
      }
    }
  },
  created() {},
  methods: {
    add() {
      console.log(this.depart)
      this.edit({})
    },
    edit(record) {
      this.form.resetFields()
      this.model = Object.assign({}, record)
      this.visible = true
      this.$nextTick(() => {
        this.form.setFieldsValue(
          pick(this.model, 'departName', 'dutyPhone', 'job', 'mobile', 'name', 'officePhone', 'orgName')
        )
        //时间格式化
      })
    },
    close() {
      this.$emit('close')
      this.visible = false
    },
    //验证办公电话
    validateOfficePhone(rule, value, callback) {
      if (value.length>2) {
        callback()
      } else {
        callback('请输入正确的电话号码！')
      }
    },
    //验证手机号
    validateMobile(rule, value, callback) {
      var re = /^1[3456789]\d{9}$/
      if (re.test(value)) {
        callback()
      } else {
        callback('请输入正确的手机号！')
      }
    },
    handleOk(e) {
      e.preventDefault()
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
          formData.orgId = this.depart.orgId
          formData.departId = this.depart.departId
          formData.departName = this.depart.departName
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
    }
  }
}
</script>

<style lang="less" scoped>
  form {
    textarea {
      resize: none;
    }
  }

  .ant-form-item {
    margin-bottom: 0;
  }
</style>