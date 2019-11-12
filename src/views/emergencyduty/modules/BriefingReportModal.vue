<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :okButtonProps="{ props: {disabled:okButtonProps}}"
    :cancelButtonProps="{ props: {disabled:okButtonProps}}"
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
          label="标题"
          help="">
          <a-input placeholder="请输入title" v-decorator="['title', {rules: [{ required: true}]}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          :required="true"
          label="分类"
          help="">
          <a-select
            v-decorator="[ 'befType', {rules: [{ required: true}]}]"
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
          label="上报部门"
          help="">
          <j-select-depart v-decorator="['orgId',{rules: [{ required: true}]}]" :trigger-change="true"></j-select-depart>
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="上报时间"
          help="">
          <a-date-picker showTime format='YYYY-MM-DD HH:mm:ss' v-decorator="[ 'reportTime', {rules: [{ required: true}]}]" />
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="正文">
          <a-textarea :rows="15" placeholder="请输入正文" v-decorator="['context', {}]" />
        </a-form-item>


      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import { httpAction } from '@/api/manage'
  import pick from '_lodash.pick@4.4.0@lodash.pick'
  import moment from "moment"
  import JSelectDepart from '@/components/jeecgbiz/JSelectDepart'

  export default {
    name: "BriefingReportModal",
    components: {JSelectDepart},
    data () {
      return {
        title:"操作",
        visible: false,
        require:true,
        model: {},
        okButtonProps:false,
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
          this.form.setFieldsValue(pick(this.model,'context','reportTime','title','befType','orgId'))
		  //时间格式化
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
            formData.publishTime = formData.publishTime?formData.publishTime.format('YYYY-MM-DD HH:mm:ss'):null;
            formData.reportTime = formData.reportTime?formData.reportTime.format('YYYY-MM-DD HH:mm:ss'):null;

            formData.reviewStatus=0;
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
  .tableList {
    .ant-table-small > .ant-table-content > .ant-table-body > table {
      table-layout: fixed;
    }
    .ant-table-small > .ant-table-content > .ant-table-body > table > .ant-table-tbody > tr > td {
      width: 100px;
      overflow: hidden;
      text-overflow: ellipsis;
    }
  }
  form {
    textarea {
      resize: none;
    }
  }

  .ant-form-item {
    margin-bottom: 0;
  }
</style>