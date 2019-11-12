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
          help=""
          label="名称">
          <a-input placeholder="名称" v-decorator="['materialName', {rules: [{ required: true}]}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          help=""
          label="别名">
          <a-input placeholder="请输入别名" v-decorator="['alias', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          help=""
          label="所属单位名称">
          <a-input placeholder="请输入所属单位名称" v-decorator="['repertoryName', {rules: [{ required: true}]}]" />
        </a-form-item>

        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              help=""
              label="类型">
              <a-input placeholder="请输入类型" v-decorator="['materialType', {rules: [{ required: true}]}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="规格型号">
              <a-input placeholder="请输入规格型号" v-decorator="['model', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="计量单位">
              <a-input placeholder="请输入计量单位" v-decorator="['measureUnit', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="物资数量">
              <a-input-number v-decorator="[ 'materialCnt', {}]" />
            </a-form-item>
          </a-col>
        </a-row>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="物资描述">
          <a-input placeholder="请输入物资描述" v-decorator="['materialDesc', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="备注">
          <a-input placeholder="请输入备注" v-decorator="['notes', {}]" />
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
    name: "MaterialModal",
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
          add: "/EmergencySource/material/add",
          edit: "/EmergencySource/material/edit",
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
          this.form.setFieldsValue(pick(this.model,'alias','materiaDesc','materialCnt','materialId','materialName','materialType','measureUnit','model','notes','repertoryId','repertoryName'))
		  //时间格式化
        });

      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            }
            let formData = Object.assign(this.model, values);
            //时间格式化
            
            console.log(formData)
            httpAction(httpurl,formData,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
              that.close();
            })



          }
        })
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