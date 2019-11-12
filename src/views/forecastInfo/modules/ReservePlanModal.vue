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
          label="预案名称">
          <a-input placeholder="请输入预案名称" :disabled="true" v-decorator="['planName', {}]" />
        </a-form-item>

        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="预案分类">
              <a-select
                v-decorator="[ 'typeCode', validatorRules.typeCode]"
                :disabled="true"
                placeholder="选择分类">
                <a-select-option value="0">总体预案</a-select-option>
                <a-select-option value="1">专项预案</a-select-option>
                <a-select-option value="2">部门预案</a-select-option>
                <a-select-option value="3">重大活动预案</a-select-option>
                <a-select-option value="4">镇街预案</a-select-option>
                <a-select-option value="5">待修预案</a-select-option>
                <a-select-option value="6">其他预案</a-select-option>
              </a-select>
            </a-form-item>

          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="版本状态编码">
              <a-input placeholder="请输入版本状态编码" :disabled="true" v-decorator="['verStateCode', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="发布单位">
          <a-input placeholder="请输入发布单位" :disabled="true" v-model="model.departName"/>
        </a-form-item>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="发布日期">
              <a-date-picker showTime format='YYYY-MM-DD HH:mm:ss' :disabled="true" v-decorator="[ 'publishDate', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="行政区划代码">
              <a-input placeholder="请输入行政区划代码" :disabled="true" v-decorator="['districtId', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="预案内容">
          <a-textarea :rows="4" placeholder="请输入预案内容" :disabled="true" v-decorator="['content', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="备注">
          <a-textarea :rows="4" placeholder="请输入备注" :disabled="true" v-decorator="['notes', {}]" />
        </a-form-item>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from "moment"

  export default {
    name: "ReservePlanModal",
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
        },
        url: {
          add: "/EventProcessing/reservePlan/add",
          edit: "/EventProcessing/reservePlan/edit",
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
          this.form.setFieldsValue(pick(this.model,'content','districtId','notes','planName','publishOrg','verStateCode','typeCode','publishDate','departName'))
		  //时间格式化
          this.form.setFieldsValue({publishDate:this.model.publishDate?moment(this.model.publishDate):null})
        });

      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        this.close();
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