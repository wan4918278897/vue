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
          <a-input placeholder="请输入名称" v-decorator="['name', {rules: [{ required: true}]}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          help=""
          label="主管部门">
          <a-input placeholder="请输入主管部门" v-decorator="['supervisalDep', {rules: [{ required: true}]}]" />
        </a-form-item>
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
              help=""
              label="救援队类型">
              <a-input placeholder="请输入救援队类型" v-decorator="['teamType', {rules: [{ required: true}]}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="成立时间">
              <a-date-picker showTime format='YYYY-MM-DD HH:mm:ss' v-decorator="[ 'foundedDate', {}]" />
            </a-form-item>
          </a-col>
        </a-row>

        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="经度">
              <a-input placeholder="请输入经度" v-decorator="['longitude', {}]" :disabled="true" />
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
              label="行政区划代码">
              <a-input placeholder="请输入行政区划代码" v-decorator="['districtId', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="负责人">
              <a-input placeholder="请输入负责人" v-decorator="['principal', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="负责人电话">
              <a-input placeholder="请输入负责人电话" v-decorator="['principalTel', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 9}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 13}}"
              label="负责人移动电话">
              <a-input placeholder="请输入负责人移动电话" v-decorator="['mobileTel', {}]" />
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
              label="传真">
              <a-input placeholder="请输入传真" v-decorator="['fax', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="现有人数">
              <a-input-number v-decorator="[ 'memberCnt', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="人员编制数">
              <a-input-number v-decorator="[ 'employees', {} ]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="主要装备描述">
          <a-input placeholder="请输入主要装备描述" v-decorator="['equipDesc', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="专长描述">
          <a-input placeholder="请输入专长描述" v-decorator="['skilled', {}]" />
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
    name: "RescueModal",
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
        employees:{rules: [{ required: true, message: '请输入employees!' }]},
        },
        url: {
          add: "/EmergencySource/rescue/add",
          edit: "/EmergencySource/rescue/edit",
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
          this.form.setFieldsValue(pick(this.model,'address','districtId','dutyTel','employees','equipDesc','fax','lngitude','ltitude','memberCnt','mobileTel','name','notes','principal','principalTel','skilled','supervisalDep','teamId','teamType'))
		  //时间格式化
          this.form.setFieldsValue({foundedDate:this.model.foundedDate?moment(this.model.foundedDate):null})
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
            formData.foundedDate = formData.foundedDate?formData.foundedDate.format('YYYY-MM-DD HH:mm:ss'):null;
            
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