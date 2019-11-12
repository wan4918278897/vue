<!--事件上报添加页面-->
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
    okText="提交"
    cancelText="关闭">
    <!-- :footer="footer" -->
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <div class="card-container">
          <a-tabs type="card" v-model="tabsActiceKey">
            <a-tab-pane tab="基本信息" key="1">
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="事件标题">
                <a-input placeholder="请输入事件标题" v-decorator="['title', {}]" disabled />
              </a-form-item>

              <a-row :gutter="10" class="noMargin">
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

              <a-row :gutter="10" class="noMargin">
                <a-col :md="10" :sm="10" :offset="2">
                  <a-form-item :labelCol="newLabelCol" :wrapperCol="newWrapperCol" label="事发时间">
                    <a-date-picker
                      showTime
                      format="YYYY-MM-DD HH:mm:ss"
                      v-decorator="[ 'date', {}]"
                      disabled
                    />
                  </a-form-item>
                </a-col>
                <a-col :md="10" :sm="10">
                  <a-form-item :labelCol="newLabelCol" :wrapperCol="newWrapperCol" label="行政区划">
                    <a-input
                      placeholder="请输入行政区划"
                      v-decorator="['administrativeDivision', {}]"
                      disabled
                    />
                  </a-form-item>
                </a-col>
              </a-row>

              <a-row :gutter="10" class="noMargin">
                <a-col :md="10" :sm="10" :offset="2">
                  <a-form-item :labelCol="newLabelCol" :wrapperCol="newWrapperCol" label="事发地点">
                    <a-input
                      type="text"
                      placeholder="请输入事发地点"
                      v-decorator="['place', {}]"
                      disabled
                    />
                  </a-form-item>
                </a-col>
                <a-col :md="10" :sm="10">
                  <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="事件级别" help="">
                    <a-select
                      placeholder="请输入事件级别"
                      v-decorator="['evenLevel', {rules: [{ required: true}]}]"
                      disabled >
                      <a-select-option v-for="(item, key) in evenLevel"
                                       :key="key"
                                       :value="item.id">{{item.eventName}}
                      </a-select-option>
                    </a-select>
                  </a-form-item>
                </a-col>
              </a-row>

              <a-row :gutter="10" class="noMargin">
                <a-col :md="10" :sm="10" :offset="2">
                  <a-form-item :labelCol="newLabelCol" :wrapperCol="newWrapperCol" label="指定用户">
                    <a-select
                      mode="multiple"
                      style="width: 100%"
                      placeholder="请选择用户"
                      v-model="selectedUser"
                      @dropdownVisibleChange="onSearch"
                      disabled
                    ></a-select>
                  </a-form-item>
                </a-col>
                <a-col :md="10" :sm="10">
                  <a-form-item :labelCol="newLabelCol" :wrapperCol="newWrapperCol" label="预案名称">
                    <a-input placeholder="请输入预案名称" v-decorator="['prePlanName', {}]" disabled />
                  </a-form-item>
                </a-col>
              </a-row>
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="事发原因">
                <a-textarea :rows="3" placeholder="请输入事发原因" v-decorator="['reason', {}]" disabled />
              </a-form-item>
              <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="事件描述">
                <a-textarea
                  :rows="3"
                  placeholder="请输入事件描述"
                  v-decorator="['evenDescribe', {}]"
                  disabled
                />
              </a-form-item>

              <a-row :gutter="10" class="lastRow">
                <a-col :md="7" :sm="10" :offset="2">
                  <a-form-item :labelCol="sanLabelCol" :wrapperCol="sanWrapperCol" label="死亡人数">
                    <a-input-number class="col3" v-decorator="[ 'deathNum', {}]" disabled />
                  </a-form-item>
                </a-col>
                <a-col :md="7" :sm="10">
                  <a-form-item :labelCol="sanLabelCol" :wrapperCol="sanWrapperCol" label="受伤人数">
                    <a-input-number class="col3" v-decorator="[ 'injuredNum', {}]" disabled />
                  </a-form-item>
                </a-col>
                <a-col :md="7" :sm="10">
                  <a-form-item :labelCol="sanLabelCol" :wrapperCol="sanWrapperCol" label="失踪人数">
                    <a-input-number class="col3" v-decorator="[ 'missNum', {}]" disabled />
                  </a-form-item>
                </a-col>
              </a-row>

              <a-card class="cardFirst" v-if="isDealInformation">
                <a-form-item
                  :labelCol="cardLabelCol"
                  :wrapperCol="cardWrapperCol"
                  label="处理过程"
                >
                  <a-textarea placeholder="请输入处理过程" v-model="process" :disabled="infoText" />
                </a-form-item>
                <a-form-item :labelCol="cardLabelCol" :wrapperCol="cardWrapperCol" label="处理结果">
                  <a-textarea placeholder="请输入处理结果" v-model="result" :disabled="infoText" />
                </a-form-item>
              </a-card>

              <a-card class="cardLast" v-if="isAuditing">
                <a-form-item :labelCol="cardLabelCol" :wrapperCol="cardWrapperCol" label="审核过程">
                  <a-textarea placeholder="请输入审核过程" v-model="adProcess" :disabled="autText" />
                </a-form-item>
                <a-form-item :labelCol="cardLabelCol" :wrapperCol="cardWrapperCol" label="审核结果">
                  <a-textarea placeholder="请输入审核结果" v-model="adResult" :disabled="autText" />
                </a-form-item>
              </a-card>
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
import { httpAction, getAction } from '@/api/manage'
import pick from 'lodash.pick'
import moment from 'moment'
import fileUpload from '../../../components/fileUpload/fileUpload'

// 引入搜索部门弹出框的组件
import { ACCESS_TOKEN } from '@/store/mutation-types'

export default {
  name: 'EventDealModal',
  components: {
    fileUpload
  },
  data() {
    return {
      title: '操作',
      visible: false,
      tabsActiceKey: '1',
      model: {},
      okButtonProps: false,
      checkedDepartKeys: [],
      checkedDepartNames: [], // 保存部门的名称 =>title
      userDepartModel: {userId: '', selectedUser: []}, // 保存SysUserDepart的用户部门中间表数据需要的对象
      selectedUser: [],
      userIds: [],
      isDealInformation: false, //是否展示处理页面
      isAuditing: false, //是否展示审核页面
      process: '', //处理 过程
      result: '', //处理 结果
      adProcess: '', //审核 过程
      adResult: '', //审核 结果
      dealType: '', //1:处理页面 2:审核页面
      reportId: '', //上报事件Id
      relateId: '', //附件表关联的事件Id
      relateType: '1', //1表示事件上传类型
      infoText: false, //处理意见框是否能编辑
      autText: false, //审核框是否能编辑
      evenType: [],
      evenLevel: [],
      evenProperty: [],
      dealInformationId: '',
      labelCol: {
        xs: {span: 24},
        sm: {span: 5}
      },
      wrapperCol: {
        xs: {span: 24},
        sm: {span: 16}
      },
      newLabelCol: {
        xs: {span: 24},
        sm: {span: 6}
      },
      newWrapperCol: {
        xs: {span: 24},
        sm: {span: 15}
      },
      cardLabelCol: {
        xs: {span: 24},
        sm: {span: 4}
      },
      cardWrapperCol: {
        xs: {span: 24},
        sm: {span: 18}
      },
      sanLabelCol: {
        xs: {span: 24},
        sm: {span: 9}
      },
      sanWrapperCol: {
        xs: {span: 24},
        sm: {span: 12}
      },
      confirmLoading: false,
      form: this.$form.createForm(this),
      validatorRules: {},
      url: {
        auditingDeal: '/even/evenAuditing/add',
        informationDeal: '/even/eventDealInformation/add',
        queryByReportId: '/even/eventBacklog/queryByReportId',
        findInformation: '/even/eventDealInformation/findInformation',
        findAud: '/even/eventDealInformation/findAud',
        queryEventSetting: '/even/eventReport/selectValue'

      }
    }
  },
    methods: {

      open(record, dealType) {
        this.dealType = dealType;
        this.reportId = record.id;
        this.relateId = record.id;
        this.getEventType()

        if (dealType == 1) {
          this.isDealInformation = true;
        } else if (dealType == 2) {
          this.isDealInformation = true;
          this.isAuditing = true;
          this.infoText = true;
        } else if (dealType == 3) {
          this.isDealInformation = true;
          this.isAuditing = true;
          this.infoText = true;
          this.autText = true;
          this.okButtonProps = true;
        }
        getAction(this.url.findInformation, {reportId: this.reportId}).then((res) => {
          if (res.success) {
            console.log(res.result)
            this.adProcess = res.result.adProcess;
            this.adResult = res.result.adResult;
            this.process = res.result.process;
            this.result = res.result.result;
            this.dealInformationId = res.result.id;
          }
        });
        this.edit(record);
      },
      edit(record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.getUser(record)
      },

      getUser(record) {
        this.model = Object.assign({}, record);
        if (record.id != '') {
          getAction(this.url.queryByReportId, {id: record.id}).then((res) => {
            if (res.success) {
              console.log(res.result);
              for (var i = 0; i < res.result.length; i++) {
                this.selectedUser.push(res.result[i].realname);
              }
            }
          });
        }
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model, 'administrativeDivision', 'deathNum', 'evenDescribe', 'evenLevel', 'evenProperty', 'evenType', 'injuredNum', 'missNum', 'place', 'prePlanName', 'reason', 'title', 'userIds'))
          //时间格式化
          this.form.setFieldsValue({date: this.model.date ? moment(this.model.date) : null})
        });
      },
      close() {
        this.$emit('close');
        this.selectedUser = [];
        this.visible = false;
        this.tabsActiceKey = '1'
        this.okButtonProps = false;

      },
      handleOk() {
        const that = this;
        // 触发表单验证
        that.confirmLoading = true;
        let httpurl = '';
        let method = 'post';
        let param = {
          reportId: this.reportId,
          process: this.process,
          result: this.result
        };
        if (this.dealType == 1) {
          httpurl += this.url.informationDeal;
        } else if (this.dealType == 2) {
          param.process = this.adProcess;
          param.result = this.adResult;
          param.dealInformationId = this.dealInformationId
          httpurl += this.url.auditingDeal;
        }

      //时间格式化
      httpAction(httpurl, param, method)
        .then(res => {
          if (res.success) {
            that.$message.success(res.message)
            that.$emit('ok')
          } else {
            that.$message.warning(res.message)
          }
        })
        .finally(() => {
          that.confirmLoading = false
          that.close()
        })
    },
    handleCancel() {
      this.close()
    },
    // 搜索用户对应的部门API
    onSearch() {
      this.$refs.departView.add()
    },
    modalFormOk(userList) {
      this.selectedUser = userList.selectedUser
      this.userIds = userList.userId
      console.log(userList)
    },
    /*
      根据部门选择用户选择用户
       */
    selectUserIds: function(record) {
      this.$refs.QueryUserListModal.open(this.selectedDepartKeys)
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
  }
}
</script>

<style lang="less" scoped>
form {
  .noMargin {
    margin-left: 20px !important;
  }
  textarea {
    resize: none;
  }
  .lastRow {
    margin-left: 10px !important;
  }
}
.cardFirst {
  background: #e5f1f5;
  border:none;
}
.cardLast {
  background: #eef7e5;
  border: none;
}
.ant-form-item {
  margin-bottom: 0;
}
</style>