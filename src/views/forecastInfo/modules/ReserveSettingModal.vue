<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">

    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-row :gutter="10">
          <a-col :md="15" :sm="20">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="预案类型">
              <a-input placeholder="请输入预案类型" v-decorator="['name', {}]"/>
            </a-form-item>
          </a-col>
          <a-col :md="9" :sm="20">

            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="排序">
              <a-input-number v-decorator="[ 'sort', {}]"/>
            </a-form-item>
          </a-col>
        </a-row>
        <!--<a-form-item-->
        <!--:labelCol="labelCol"-->
        <!--:wrapperCol="wrapperCol"-->
        <!--label="type">-->
        <!--<a-input placeholder="请输入type" v-decorator="['type', {}]" />-->
        <!--</a-form-item>-->

      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import {httpAction} from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from "moment"

  export default {
    name: "ReserveSettingModal",
    data() {
      return {
        title: "操作",
        visible: false,
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
          add: "/forecastInfo/reserveSetting/add",
          edit: "/forecastInfo/reserveSetting/edit",
        },
      }
    },
    created() {
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
          this.form.setFieldsValue(pick(this.model, 'name', 'sort', 'type'))
          //时间格式化
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


    }
  }
</script>

<style lang="less" scoped>

</style>