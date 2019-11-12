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
          <a-input placeholder="请输入名称" v-decorator="['orgName', {rules: [{ required: true}]}]" />
        </a-form-item>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              help=""
              label="机构分类">
              <a-input placeholder="请输入机构分类" v-decorator="['orgType', {rules: [{ required: true}]}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              help=""
              label="行政区划代码">
              <a-input placeholder="请输入行政区划代码" v-decorator="['refDistrictId', {rules: [{ required: true}]}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          help=""
          label="地址">
          <a-input placeholder="请输入地址" v-decorator="['address', {rules: [{ required: true}]}]" />
        </a-form-item>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="经度">
              <a-input placeholder="请输入经度" v-decorator="['longitude', {}]" :disabled="true"/>
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="纬度">
              <a-input placeholder="请输入纬度" v-decorator="['latitude', {}]" :disabled="true"/>
            </a-form-item>
          </a-col>
        </a-row>

        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="邮编">
              <a-input placeholder="请输入邮编" v-decorator="['postCode', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              help=""
              label="应急办领导">
              <a-input placeholder="请输入应急办领导" v-decorator="['firstLeader', {rules: [{ required: true}]}]" />
            </a-form-item>
          </a-col>
        </a-row>

        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span:7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              help=""
              label="领导联系电话">
              <a-input placeholder="请输入应急办领导联系电话" v-decorator="['firstTel', {rules: [{ required: true}]}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="领导移动电话">
              <a-input placeholder="请输入应急办领导移动电话" v-decorator="['firstMobileTel', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="值班电话">
              <a-input placeholder="请输入值班电话" v-decorator="['dutyTel', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="值班传真">
              <a-input placeholder="请输入值班传真" v-decorator="['dutyFax', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="分管领导">
          <a-input placeholder="请输入分管领导" v-decorator="['secondLeader', {}]" />
        </a-form-item>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="领导联系电话">
              <a-input placeholder="请输入分管领导联系电话" v-decorator="['secondTel', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="领导移动电话">
              <a-input placeholder="请输入分管领导移动电话" v-decorator="['secondMobileTel', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="机构职责">
          <a-input placeholder="请输入机构职责" v-decorator="['orgDuty', {}]" />
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
    name: "EmorgModal",
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
          add: "/EmergencySource/emorg/add",
          edit: "/EmergencySource/emorg/edit",
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
          this.form.setFieldsValue(pick(this.model,'address','dutyFax','dutyTel','elevation','firstLeader','firstMobileTel','firstTel','latitude','longitude','notes','orgDuty','orgId','orgName','orgType','postCode','refDistrictId','secondLeader','secondMobileTel','secondTel'))
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