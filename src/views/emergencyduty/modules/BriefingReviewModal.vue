<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    :maskClosable="false"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">
    
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="标题">
          <a-input placeholder="请输入title" :disabled=true v-decorator="['title', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="分类">
          <a-select
            v-decorator="[ 'befType', validatorRules.befType]"
            :disabled=true
            placeholder="选择分类">
            <a-select-option value="0">工作动态</a-select-option>
            <a-select-option value="1">国内动态</a-select-option>
            <a-select-option value="2">典型案例</a-select-option>
            <a-select-option value="3">应急简报</a-select-option>
          </a-select>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="上报部门">
          <a-input placeholder="请输入orgId" :disabled=true v-model="model.departName"/>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="上报人">
          <a-input v-model="model.realname"  :disabled=true />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="上报时间">
          <a-date-picker showTime format='YYYY-MM-DD HH:mm:ss' v-decorator="[ 'reportTime', {}]" :disabled=true />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="正文">
          <a-textarea :rows="15" placeholder="请输入context" v-decorator="['context', {}]" :disabled=true />
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import { httpAction } from '@/api/manage'
  import pick from '_lodash.pick@4.4.0@lodash.pick'
  import moment from "moment"

  export default {
    name: "BriefingReviewModal",
    data () {
      return {
        title:"操作",
        visible: false,
        model: {},
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },

        confirmLoading: false,
        form: this.$form.createForm(this),
        validatorRules:{
        type:{rules: [{ required: true, message: '请输入type!' }]},
        },
        url: {
          add: "/emergencyduty/briefing/add",
          edit: "/emergencyduty/briefing/edit",
        },
      }
    },
    created () {
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'title','context','befType'))
		  //时间格式化
          this.form.setFieldsValue({reportTime:this.model.reportTime?moment(this.model.reportTime):null})
        });

      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        this.close()
      },
      handleCancel () {
        this.close()
      },


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