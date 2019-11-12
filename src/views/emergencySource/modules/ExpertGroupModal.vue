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
          label="专家组名称">
          <a-input placeholder="请输入专家组名称" v-decorator="['groupName', {rules: [{ required: true}]}]" />
        </a-form-item>

        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              help=""
              label="组长">
              <a-input placeholder="请输入组长" v-decorator="['leader', {rules: [{ required: true}]}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              help=""
              label="联系电话">
              <a-input placeholder="请输入联系电话" v-decorator="['tel', {rules: [{ required: true}]}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              help=""
              label="第一副组长">
              <a-input placeholder="请输入第一副组长" v-decorator="['subLeader1', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              help=""
              label="第二副组长">
              <a-input placeholder="请输入第二副组长" v-decorator="['subLeader2', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          help=""
          label="成员组成">
          <a-input placeholder="请输入成员组成" v-decorator="['nationality', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          help=""
          label="工作内容">
          <a-input placeholder="请输入工作内容" v-decorator="['workUnit', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          help=""
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
    name: "ExpertGroupModal",
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
          add: "/EmergencySource/expertGroup/add",
          edit: "/EmergencySource/expertGroup/edit",
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
          this.form.setFieldsValue(pick(this.model,'groupId','groupName','leader','nationality','notes','subLeader1','subLeader2','workUnit','tel'))
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