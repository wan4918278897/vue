<!--事件上报添加页面-->
<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :okButtonProps="{ props: {disabled:okButtonProps}}"
    :cancelButtonProps="{ props: {disabled:okButtonProps}}"
    :confirmLoading="confirmLoading"

    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭"
    :maskClosable="false"
  >
    <!-- :footer="footer" -->
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <div class="card-container">
          <a-tabs type="card" v-model="tabsActiceKey">
            <a-tab-pane tab="基本信息" key="1">
              <a-row :gutter="10">
                <a-col :md="20" :sm="20" :offset="2">
                  <a-form-item :labelCol="rowLabelCol" :wrapperCol="rowWrapperCol" label="事件标题"
                               help=""
                  >
                    <a-input
                      placeholder="请输入事件标题"
                      v-decorator="['title', {rules: [{ required: true}]}]"
                    />
                  </a-form-item>
                </a-col>
              </a-row>

              <a-row :gutter="10">
                <a-col :md="10" :sm="10" :offset="2">
                  <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="事件性质" help="">

                    <a-select v-decorator="[ 'evenProperty', {}]" placeholder="事件性质" help="">
                      <a-select-option v-for="(item, key) in evenProperty"
                                       :key="key"
                                       :value="item.id">{{item.eventName}}
                      </a-select-option>
                    </a-select>
                  </a-form-item>
                </a-col>

                <a-col :md="10" :sm="10">
                  <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="事件类型">
                    <a-select v-decorator="[ 'evenType', {}]" placeholder="选择分类">
                      <a-select-option v-for="(item, key) in evenType"
                                       :key="key"
                                       :value="item.id">{{item.eventName}}
                      </a-select-option>
                    </a-select>
                  </a-form-item>
                </a-col>
              </a-row>

              <a-row :gutter="10">
                <a-col :md="10" :sm="10" :offset="2">
                  <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="事发时间" help="">
                    <a-date-picker
                      showTime
                      format="YYYY-MM-DD"
                      v-decorator="[ 'date', {rules: [{ required: true}]}]"
                    />
                  </a-form-item>
                </a-col>
                <a-col :md="10" :sm="10">
                  <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="行政区划" help="">
                    <a-input
                      placeholder="请输入行政区划"
                      v-decorator="['administrativeDivision', {rules: [{ required: true}]}]"
                    />
                  </a-form-item>
                </a-col>
              </a-row>

              <a-row :gutter="10">
                <a-col :md="10" :sm="10" :offset="2">
                  <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="指定用户">
                    <a-select
                      mode="multiple"
                      style="width: 100%"
                      placeholder="请选择用户"
                      v-model="selectedUser"
                      @dropdownVisibleChange="onSearch"
                    ></a-select>
                  </a-form-item>
                </a-col>
                <a-col :md="10" :sm="10">
                  <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="事件级别" help="">
                    <a-select
                      placeholder="请输入事件级别"
                      v-decorator="['evenLevel', {rules: [{ required: true}]}]"
                    >
                      <a-select-option v-for="(item, key) in evenLevel"
                                       :key="key"
                                       :value="item.id">{{item.eventName}}
                      </a-select-option>
                    </a-select>
                  </a-form-item>
                </a-col>
              </a-row>

              <a-row :gutter="10">
                <a-col :md="10" :sm="10" :offset="2">
                  <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="上报人">
                    <a-input type="text" placeholder="请输入上报人姓名" v-decorator="['name', {}]"/>
                  </a-form-item>
                </a-col>
                <a-col :md="10" :sm="10">
                  <a-form-item
                    :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
                    :wrapperCol="{ xs: {span: 24}, sm: {span: 14}}"
                    label="上报人电话"
                  >
                    <a-input type="text" placeholder="请输入上报人电话" v-decorator="['phone', {}]"/>
                  </a-form-item>
                </a-col>
              </a-row>

              <a-row :gutter="10">
                <a-col :md="20" :sm="20" :offset="2">
                  <a-form-item :labelCol="rowLabelCol" :wrapperCol="rowWrapperCol" label="事发地点" help="">
                    <a-input
                      type="text"
                      placeholder="请输入事发地点"
                      v-decorator="['place', {rules: [{ required: true}]}]"
                    />
                  </a-form-item>
                </a-col>
              </a-row>
              <a-row :gutter="10">
                <a-col :md="20" :sm="20" :offset="2">
                  <a-form-item :labelCol="rowLabelCol" :wrapperCol="rowWrapperCol" label="事发原因" help="">
                    <a-textarea
                      :rows="3"
                      placeholder="请输入事发原因"
                      v-decorator="['reason', {rules: [{ required: true}]}]"
                    />
                  </a-form-item>
                </a-col>
              </a-row>
              <a-row :gutter="10">
                <a-col :md="20" :sm="20" :offset="2">
                  <a-form-item :labelCol="rowLabelCol" :wrapperCol="rowWrapperCol" label="事件描述" help="">
                    <a-textarea
                      :rows="3"
                      placeholder="请输入事件描述"
                      v-decorator="['evenDescribe', {rules: [{ required: true}]}]"
                    />
                  </a-form-item>
                </a-col>
              </a-row>

              <a-row :gutter="10">
                <a-col :md="7" :sm="10" :offset="2">
                  <a-form-item :labelCol="newLabelCol" :wrapperCol="newWrapperCol" label="死亡人数" help="">
                    <a-input-number
                      class="col3"
                      v-decorator="[ 'deathNum', {rules: [{ required: true}]}]"
                    />
                  </a-form-item>
                </a-col>
                <a-col :md="7" :sm="10">
                  <a-form-item :labelCol="newLabelCol" :wrapperCol="newWrapperCol" label="受伤人数" help="">
                    <a-input-number
                      class="col3"
                      v-decorator="[ 'injuredNum', {rules: [{ required: true}]}]"
                    />
                  </a-form-item>
                </a-col>
                <a-col :md="7" :sm="10">
                  <a-form-item :labelCol="newLabelCol" :wrapperCol="newWrapperCol" label="失踪人数" help="">
                    <a-input-number
                      class="col3"
                      v-decorator="[ 'missNum', {rules: [{ required: true}]}]"
                    />
                  </a-form-item>
                </a-col>
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
    <departView ref="departView" @ok="modalFormOk"></departView>
  </a-modal>
</template>
<script>
  import {httpAction, getAction, postAction, deleteAction} from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from 'moment'
  import PdfPreviewModal from '@/views/jeecg/modules/PdfPreviewModal'
  import fileUpload from '../../../components/fileUpload/fileUpload'

  // 引入搜索部门弹出框的组件
  import departView from './DepartView'
  import {ACCESS_TOKEN} from '@/store/mutation-types'

  export default {
    name: 'EventReportModal',
    components: {
      departView,
      PdfPreviewModal,
      fileUpload
    },
    data() {
      return {
        title: '操作',
        visible: false,
        tabsActiceKey: '1',
        okButtonProps: false,
        model: {},
        checkedDepartKeys: [],
        checkedDepartNames: [], // 保存部门的名称 =>title
        userDepartModel: {userId: '', selectedUser: []}, // 保存SysUserDepart的用户部门中间表数据需要的对象
        selectedUser: [],
        userIds: [],
        selectedPlan: '',
        planId: '',
        relateId: '',
        relateType: '1', //1表示事件上传类型
        modalTipText: '',
        evenType: [],
        evenLevel: [],
        evenProperty: [],
        // userDepartModel: {userId: '', departIdList: []}, // 保存SysUserDepart的用户部门中间表数据需要的对象
        labelCol: {
          xs: {span: 24},
          sm: {span: 6}
        },
        wrapperCol: {
          xs: {span: 24},
          sm: {span: 15}
        },
        newLabelCol: {
          xs: {span: 24},
          sm: {span: 9}
        },
        newWrapperCol: {
          xs: {span: 24},
          sm: {span: 13}
        },
        rowLabelCol: {
          xs: {span: 24},
          sm: {span: 3}
        },
        rowWrapperCol: {
          xs: {span: 24},
          sm: {span: 20}
        },

        confirmLoading: false,
        form: this.$form.createForm(this),
        validatorRules: {},
        url: {
          add: '/even/eventReport/add',
          edit: '/even/eventReport/edit',
          getReceivePhone: '/even/eventReport/getReceivePhone',
          queryByReportId: '/even/eventBacklog/queryByReportId',
          deleteByType: '/fileManage/fileManage/deleteByType',
          createId: '/sys/user/createId',// 引入生成Id
          queryEventSetting: '/even/eventReport/selectValue'
        },
        columns: [
          {
            title: '文件名称',
            align: 'center',
            dataIndex: 'fileName'
          },
          {
            title: '文件路径',
            align: 'center',
            dataIndex: 'path'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align: 'center',
            scopedSlots: {customRender: 'action'}
          }
        ],
      }
    },
    created() {
      window.addEventListener('beforeunload', e => this.beforeunloadFn(e))
    },
    methods: {
      add() {
        getAction(this.url.getReceivePhone).then(res => {
          this.$nextTick(() => {
            //时间格式化
            this.form.setFieldsValue({phone: res})
          })
        })
        this.edit({})
      },
      edit(record) {
        console.log(record)
        this.form.resetFields()
        this.model = Object.assign({}, record)
        this.visible = true
        this.$nextTick(() => {
          this.form.setFieldsValue(
            pick(
              this.model,
              'administrativeDivision',
              'deathNum',
              'evenDescribe',
              'evenLevel',
              'evenProperty',
              'injuredNum',
              'missNum',
              'name',
              'place',
              'reason',
              'title',
              'phone',
              'evenType'
            )
          )
          //时间格式化
          this.form.setFieldsValue({date: this.model.date ? moment(this.model.date) : null})

        })
        if (record.id) {
          //编辑页面
          this.getUser(record)
          this.relateId = record.id
        } else {
          this.createId()
        }

        this.getEventType()
      },

      //编辑用户回显
      getUser(record) {
        this.model = Object.assign({}, record)
        getAction(this.url.queryByReportId, {id: record.id}).then(res => {
          if (res.success) {
            for (var i = 0; i < res.result.length; i++) {
              this.selectedUser.push(res.result[i].realname)
            }
            this.$refs.departView.tipDetail(res.result, this.userIds)
          }
        })
      },

      //获取事件类型
      getEventType() {
        getAction(this.url.queryEventSetting).then((res) => {
          for (var i = 0; i < res.length; i++) {
            if (res[i].eventType == 'A') {
              this.evenLevel.push(res[i]);
            } else if (res[i].eventType == 'B') {
              this.evenType.push(res[i]);
            } else if (res[i].eventType == 'C') {
              this.evenProperty.push(res[i]);
            }
          }

        })
      },
      close() {
        this.$emit('close')
        this.tabsActiceKey = '1'
        this.selectedUser = []
        this.phone = ''
        this.visible = false
        this.evenLevel=[];
        this.evenProperty=[];
        this.evenType=[];
      },
      error() {
        this.$message.error(this.modalTipText)
      },
      handleOk() {
        const that = this
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true
            let httpurl = ''
            let method = ''
            if (!this.model.id) {
              httpurl += this.url.add
              method = 'post'
            } else {
              httpurl += this.url.edit
              method = 'put'
            }
            let formData = Object.assign(this.model, values)

            //时间格式化
            formData.date = formData.date ? formData.date.format('YYYY-MM-DD HH:mm:ss') : null
            httpurl = httpurl + '?userIds=' + this.userIds + '&relateId=' + that.relateId
            formData.prePlanName = this.planId
            httpAction(httpurl, formData, method)
              .then(res => {
                if (res.success) {
                  that.$message.success(res.message)
                  that.$emit('ok')
                  that.close()
                } else {
                  that.$message.warning(res.message)
                }
              })
              .finally(() => {
                that.confirmLoading = false
              })
          }
        })
      },
      handleCancel() {
        this.fileDelete()
        this.close()
      },
      // 搜索用户对应的部门API
      onSearch() {
        this.$refs.departView.add()
      },
      modalFormOk(userList) {
        this.selectedUser = userList.selectedUser
        this.userIds = userList.userId
      },
      // planViewOk(planList) {
      //   console.log("--------------------------------------------")
      //   this.selectedPlan = planList.planName
      //   this.planId = planList.planId
      //
      // },
      /*
          根据部门选择用户选择用户
           */
      selectUserIds: function (record) {
        this.$refs.QueryUserListModal.open(this.selectedDepartKeys)
      },
      //生成虚拟Id
      createId() {
        //生成relateId
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