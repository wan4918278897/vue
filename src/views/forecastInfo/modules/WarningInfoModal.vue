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
          label="信息标题">
          <a-input placeholder="请输入信息标题" v-decorator="['warningTitle', {rules: [{ required: true}]}]" />
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="预警单位">
          <a-input placeholder="请输入预警单位" v-decorator="['warnOrg', {rules: [{ required: true}]}]" />
        </a-form-item>

        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="预警类别">
              <a-input placeholder="请输入预警类别" v-decorator="['warnType', {rules: [{ required: true}]}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="预警级别">
              <a-input placeholder="请输入预警级别" v-decorator="['warnLevel', {rules: [{ required: true}]}]" />
            </a-form-item>
          </a-col>
        </a-row>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="预警状态">
          <a-input placeholder="请输入预警状态" v-decorator="['warnState', {rules: [{ required: true}]}]" />
        </a-form-item>

        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="开始时间">
              <a-date-picker showTime format='YYYY-MM-DD HH:mm:ss' v-decorator="[ 'beginTime', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="结束时间">
              <a-date-picker showTime format='YYYY-MM-DD HH:mm:ss' v-decorator="[ 'endTime', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="影响范围">
          <a-input placeholder="请输入影响范围" v-decorator="['impactScope', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="影响范围行政区划">
          <a-input placeholder="请输入影响范围行政区划" v-decorator="['districtId', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="防范措施">
          <a-input placeholder="请输入防范措施" v-decorator="['preventStep', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="危害程度">
          <a-input placeholder="请输入危害程度" v-decorator="['imperilDegree', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="影响方式">
          <a-input placeholder="请输入影响方式" v-decorator="['impactMethod', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="上报单位">
          <a-input placeholder="请输入上报单位" v-decorator="['reportOrg', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="上报时间">
          <a-date-picker showTime format='YYYY-MM-DD HH:mm:ss' v-decorator="[ 'reportTime', {}]" />
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
    name: "WarningInfoModal",
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
          add: "/EventProcessing/warningInfo/add",
          edit: "/EventProcessing/warningInfo/edit",
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
          this.form.setFieldsValue(pick(this.model,'districtId','impactMethod','impactScope','imperilDegree','receiveId','preventStep','reportOrg','warnId','warnLevel','warnOrg','warnState','warnType','warningTitle'))
		  //时间格式化
          this.form.setFieldsValue({beginTime:this.model.beginTime?moment(this.model.beginTime):null})
          this.form.setFieldsValue({endTime:this.model.endTime?moment(this.model.endTime):null})
          this.form.setFieldsValue({reportTime:this.model.reportTime?moment(this.model.reportTime):null})
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
            formData.beginTime = formData.beginTime?formData.beginTime.format('YYYY-MM-DD HH:mm:ss'):null;
            formData.endTime = formData.endTime?formData.endTime.format('YYYY-MM-DD HH:mm:ss'):null;
            formData.reportTime = formData.reportTime?formData.reportTime.format('YYYY-MM-DD HH:mm:ss'):null;
            
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