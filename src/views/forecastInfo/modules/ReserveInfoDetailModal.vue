<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    :footer="null"
    @cancel="handleCancel"
    cancelText="关闭">

    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-row :gutter="10">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="预案名称">
              <a-input placeholder="请输入预案名称" v-decorator="['name', {}]" disabled/>
            </a-form-item>
        </a-row>

        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="预案代号">
              <a-input placeholder="请输入预案代号" v-decorator="['code', {}]" disabled/>
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="版本号">
              <a-input placeholder="请输入版本号" v-decorator="['version', {}]" disabled/>
            </a-form-item>
          </a-col>
        </a-row>

        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="预案分类">
              <a-select v-decorator="[ 'type', {}]" placeholder="预案分类" help="" disabled>

                <a-select-option v-for="(item, key) in reserveType"
                                 :key="key"
                                 :value="item.id">{{item.name}}
                </a-select-option>
              </a-select>
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="发布时间">
              <a-date-picker showTime format='YYYY-MM-DD' v-decorator="[ 'date', {}]" disabled/>
            </a-form-item>
          </a-col>
        </a-row>

        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="行政区划">
              <a-input placeholder="请输入行政区划" v-decorator="['administrativeDivision', {}]" disabled/>
            </a-form-item>
          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="发布单位">
              <a-input placeholder="请输入发布单位" v-model="model.departName" disabled/>
            </a-form-item>
          </a-col>
        </a-row>

        <a-row :gutter="10">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="内容">
              <a-textarea placeholder="请输入内容" v-decorator="['content', {}]" disabled></a-textarea>
            </a-form-item>
        </a-row>

        <a-row :gutter="10">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="备注">
              <a-input placeholder="请输入备注" v-decorator="['remark', {}]" disabled/>
            </a-form-item>
        </a-row>

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import {httpAction, getAction} from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from "moment"
  import JSelectDepart from '@/components/jeecgbiz/JSelectDepart'

  export default {
    name: "ReserveInfoModal",
    components:
       {
           JSelectDepart
       },
    data() {
      return {
        title: "操作",
        visible: false,
        reserveType: [],
        model: {},
        labelCol: {
          xs: {span: 24},
          sm: {span: 5},
        },
        wrapperCol: {
          xs: {span: 24},
          sm: {span: 16},
        },

        confirmLoading: false,
        form: this.$form.createForm(this),
        validatorRules: {},
        url: {
          add: "/forecastInfo/reserveInfo/add",
          edit: "/forecastInfo/reserveInfo/edit",
          getReserveSetting: "/forecastInfo/reserveInfo/getReserveSetting",

        },
      }
    },
    created() {
      this.getReserveSetting();
    },
    methods: {
      add() {
        this.edit({});
      },
      edit(record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model, 'administrativeDivision', 'content', 'name', 'orgId', 'remark', 'type', 'code', 'version', 'departName'))
          //时间格式化
          this.form.setFieldsValue({date: this.model.date ? moment(this.model.date) : null})
        });

      },
      close() {
        this.$emit('close');
        this.visible = false;
      },
      handleOk() {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if (!this.model.id) {
              httpurl += this.url.add;
              method = 'post';
            } else {
              httpurl += this.url.edit;
              method = 'put';
            }
            let formData = Object.assign(this.model, values);
            //时间格式化
            formData.date = formData.date ? formData.date.format('YYYY-MM-DD HH:mm:ss') : null;

            console.log(formData)
            httpAction(httpurl, formData, method).then((res) => {
              if (res.success) {
                that.$message.success(res.message);
                that.$emit('ok');
              } else {
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
              that.close();
            })


          }
        })
      },
      handleCancel() {
        this.close()
      },
      getReserveSetting() {
        getAction(this.url.getReserveSetting).then(res => {
          this.reserveType = res;
        })
      }

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