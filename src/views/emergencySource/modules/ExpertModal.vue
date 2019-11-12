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
          label="专家姓名">
          <a-input placeholder="请输入专家姓名" v-decorator="['name', {rules: [{ required: true}]}]" />
        </a-form-item>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              help=""
              label="专家类别">
              <a-input placeholder="请输入专家类别" v-decorator="['expertType', {rules: [{ required: true}]}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="性别">
              <a-input placeholder="请输入性别" v-decorator="['sex', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="出生日期">
              <a-date-picker showTime format='YYYY-MM-DD HH:mm:ss' v-decorator="[ 'birth', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="民族">
              <a-input placeholder="请输入民族" v-decorator="['nationality', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="工作单位">
          <a-input placeholder="请输入工作单位" v-decorator="['workOrg', {}]" />
        </a-form-item>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="职称">
              <a-input placeholder="请输入职称" v-decorator="['post', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">

            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="行政职务">
              <a-input placeholder="请输入行政职务" v-decorator="['headship', {}]" />
            </a-form-item>
          </a-col>
        </a-row>


        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              help=""
              label="专业类别">
              <a-input placeholder="请输入专业类别" v-decorator="['major',  {rules: [{ required: true}]}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="单位邮编">
              <a-input placeholder="请输入单位邮编" v-decorator="['postcode', {}]" />
            </a-form-item>
          </a-col>
        </a-row>

        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              help=""
              label="移动电话">
              <a-input placeholder="请输入移动电话" v-decorator="['mobileTel', {rules: [{ required: true}]}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">

            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="办公电话">
              <a-input placeholder="请输入办公电话" v-decorator="['officeTel', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="传真">
              <a-input placeholder="请输入传真" v-decorator="['fax', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="电子邮箱">
              <a-input placeholder="请输入电子邮箱" v-decorator="['email', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="家庭电话">
              <a-input placeholder="请输入家庭电话" v-decorator="['homeTel', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">

            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="家庭住址">
              <a-input placeholder="请输入家庭住址" v-decorator="['homeAddr', {}]" />
            </a-form-item>
          </a-col>
        </a-row>

        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="证件类型">
              <a-input placeholder="请输入证件类型" v-decorator="['cerType', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="证件号码">
              <a-input placeholder="请输入证件号码" v-decorator="['cerNo', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="籍贯">
              <a-input placeholder="请输入籍贯" v-decorator="['nativePlace', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="参加工作时间">
              <a-date-picker showTime format='YYYY-MM-DD HH:mm:ss' v-decorator="[ 'joinTime', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="政治面貌">
              <a-input placeholder="请输入政治面貌" v-decorator="['party', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="健康状况">
              <a-input placeholder="请输入健康状况" v-decorator="['health', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="最高学历">
              <a-input placeholder="请输入最高学历" v-decorator="['qualification', {}]" />
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">

            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="毕业院校">
              <a-input placeholder="请输入毕业院校" v-decorator="['graduate', {}]" />
            </a-form-item>
          </a-col>
        </a-row>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="工作单位主管部门">
          <a-input placeholder="请输入工作单位主管部门" v-decorator="['supervisalDep', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="通信地址">
          <a-input placeholder="请输入通信地址" v-decorator="['postalAddress', {}]" />
        </a-form-item>


        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="户籍所在地">
          <a-input placeholder="请输入户籍所在地" v-decorator="['domicile', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="专业特长描述">
          <a-input placeholder="请输入专业特长描述" v-decorator="['proficient', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="工作简历概述">
          <a-input placeholder="请输入工作简历概述" v-decorator="['resume', {}]" />
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="所属专家组">
          <a-input placeholder="请输入所属专家组" v-decorator="['groupId', {}]" />
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
    name: "ExpertModal",
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
          add: "/EmergencySource/expert/add",
          edit: "/EmergencySource/expert/edit",
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
          this.form.setFieldsValue(pick(this.model,'cerNo','cerType','domicile','email','expertId','expertType','fax','graduate','groupId','headship','health','homTel','homeAddr','major','mobileTel','name','nationality','nativePlace','notes','officeTel','party','post','postalAddress','postcode','proficient','qualification','resume','sex','supervisalDep','workOrg'))
		  //时间格式化
          this.form.setFieldsValue({birth:this.model.birth?moment(this.model.birth):null})
          this.form.setFieldsValue({joinTime:this.model.joinTime?moment(this.model.joinTime):null})
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
            formData.birth = formData.birth?formData.birth.format('YYYY-MM-DD HH:mm:ss'):null;
            formData.joinTime = formData.joinTime?formData.joinTime.format('YYYY-MM-DD HH:mm:ss'):null;
            
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