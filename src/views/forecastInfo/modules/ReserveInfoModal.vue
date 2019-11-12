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
        <div class="card-container">
          <a-tabs type="card" v-model="tabsActiceKey">
            <a-tab-pane tab="基本信息" key="1">
              <a-row >
                  <a-form-item
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                    help=""
                    label="预案名称">
                    <a-input placeholder="请输入预案名称" v-decorator="['name', {rules: [{ required: true}]}]"/>
                  </a-form-item>
              </a-row >
              <a-row :gutter="10">
                <a-col :md="10" :sm="10" :offset="2">
                  <a-form-item
                    :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
                    :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
                    label="预案代号">
                    <a-input placeholder="请输入预案代号" v-decorator="['code', {}]"/>
                  </a-form-item>
                </a-col>
                <a-col :md="10" :sm="10">
                  <a-form-item
                    :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
                    :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
                    label="版本号">
                    <a-input placeholder="请输入版本号" v-decorator="['version', {}]"/>
                  </a-form-item>
                </a-col>
              </a-row>

              <a-row :gutter="10">
                <a-col :md="10" :sm="10" :offset="2">
                  <a-form-item
                    :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
                    :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
                    help=""
                    label="预案分类">
                    <a-select v-decorator="[ 'type', {rules: [{ required: true}]}]" placeholder="预案分类">

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
                    label="发布时间"
                    help="">
                    <a-date-picker showTime format='YYYY-MM-DD' v-decorator="[ 'date', {rules: [{ required: true}]}]"/>
                  </a-form-item>
                </a-col>
              </a-row>

              <a-row :gutter="10">
                <a-col :md="10" :sm="10" :offset="2">
                  <a-form-item
                    :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
                    :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
                    label="行政区划">
                    <a-input placeholder="请输入行政区划" v-decorator="['administrativeDivision', {}]"/>
                  </a-form-item>
                </a-col>
                <a-col :md="10" :sm="10">
                  <a-form-item
                    :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
                    :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
                    help=""
                    label="发布单位">
                    <j-select-depart v-decorator="['publishOrg',{rules: [{ required: true}]}]" :trigger-change="true"></j-select-depart>
                  </a-form-item>
                </a-col>
              </a-row>

              <a-row >
                  <a-form-item
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                    label="内容">
                    <a-textarea placeholder="请输入内容" v-decorator="['content', {}]"/>
                  </a-form-item>
              </a-row>
              <a-row >
                  <a-form-item
                    :labelCol="labelCol"
                    :wrapperCol="wrapperCol"
                    label="备注">
                    <a-input placeholder="请输入备注" v-decorator="['remark', {}]"/>
                  </a-form-item>
              </a-row>
            </a-tab-pane>
            <a-tab-pane tab="文件上传" key="2">
              <fileUpload
                ref="fileUpload"
                :relateType="relateType"
                :relateId="relateId"
                v-if="visible"
              ></fileUpload>
            </a-tab-pane>
          </a-tabs>
        </div>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import {httpAction, getAction,deleteAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from "moment"
  import fileUpload from '../../../components/fileUpload/fileUpload'
  import JSelectDepart from '@/components/jeecgbiz/JSelectDepart'

  export default {
    name: "ReserveInfoModal",
    components: {
      fileUpload,
      JSelectDepart
    },
    data() {
      return {
        title: "操作",
        visible: false,
        relateId: '',
        relateType: '2', //1表示预案文件上传类型
        reserveType: [],
        tabsActiceKey: '1',
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
          deleteByType: '/fileManage/fileManage/deleteByType',
          createId: '/sys/user/createId',// 引入生成Id
        },
      }
    },
    created() {
      this.getReserveSetting();
      window.addEventListener('beforeunload', e => this.beforeunloadFn(e))

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
          this.form.setFieldsValue(pick(this.model, 'administrativeDivision', 'content', 'name', 'orgId', 'remark', 'type', 'code', 'version', 'publishOrg'))
          //时间格式化
          this.form.setFieldsValue({date: this.model.date ? moment(this.model.date) : null})
        });
        if (record.id) {
          //编辑页面
          this.relateId = record.id
        } else {
          this.createId()
        }

      },
      close() {
        this.$emit('close');
        this.tabsActiceKey = '1'
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
            httpurl = httpurl +'?relateId=' + that.relateId

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
        this.fileDelete()
        this.close()
      },
      getReserveSetting() {
        getAction(this.url.getReserveSetting).then(res => {
          this.reserveType = res;
        })
      },
      createId() {
        //生成userId
        getAction(this.url.createId).then(res => {
          if (res.success) {
            this.relateId = res.result
            this.visible = true
          }
        })
      },
      //关闭页面异常删除文件方法
      fileDelete() {
        let that = this
        let param = {
          relateId: that.relateId,
          relateType: that.relateType
        }
        //依据关联的类型和Id删除无用附件
        deleteAction(that.url.deleteByType, param).then(res => {
          if (!res.success) {
            that.$message.warning(res.message)
          }
        })
      },
      beforeunloadFn(e) {
        let relateId = this.relateId
        console.log('刷新或关闭2333')
        this.fileDelete(relateId)
      }
    },
    destroyed() {
      window.removeEventListener('beforeunload', e => this.beforeunloadFn(e))
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