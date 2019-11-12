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
            label="标题"
            help="">
            <a-input placeholder="请输入标题" v-decorator="['title', {rules: [{ required: true}]}]" />
          </a-form-item>

        <a-row :gutter="10">
          <a-col :md="10" :sm="10" :offset="2">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="值班日期"
              help="">
              <a-date-picker showTime format='YYYY-MM-DD' v-decorator="[ 'dutyDate', {rules: [{ required: true}]}]" />
            </a-form-item>

          </a-col>
          <a-col :md="10" :sm="10">
            <a-form-item
              :labelCol="{ xs: {span: 24}, sm: {span: 7}}"
              :wrapperCol="{ xs: {span: 24}, sm: {span: 15}}"
              label="班次">
              <a-select initialValue="早班" style="width: 100%" v-model="scheduleId">
                <a-select-option
                  v-for="(item,index) in scheduleType"
                  :key="index"
                  :value="item.id"
                >{{item.scheduleType_dictText}}</a-select-option>
              </a-select>
            </a-form-item>

          </a-col>
        </a-row>



        <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="查看人">
          <a-select
            mode="multiple"
            style="width: 100%"
            placeholder="请选择查看人"
            v-model="selectedUser"
            @dropdownVisibleChange="onSearch"
          ></a-select>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="值班记录">
          <a-textarea :rows="4" placeholder="请输入值班记录" v-decorator="['dutyLog', {}]" />
        </a-form-item>

      </a-form>
    </a-spin>
    <!-- 员工列表 -->
    <staffList ref="staffList" @choseUser="choseUser"></staffList>
  </a-modal>
</template>

<script>
  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import moment from "moment"
  import { getAction } from '@/api/manage'
  import staffList from '../../../components/fullcalendar/staffList'

  export default {
    name: "ScheduleJournalModal",
    components: {
      staffList
    },
    data () {
      return {
        title:"操作",
        visible: false,
        scheduleType: [],
        selectedUser: [],
        scheduleId: '',
        userIds : [],
        viewer:'',
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
          add: "/scheduling/scheduleJournal/add",
          edit: "/scheduling/scheduleJournal/edit",
          dataList: '/scheduling/scheduleDefine/list',
          queryDealList: '/scheduling/scheduleJournal/findSchedulePerson',
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

        console.log("///////////////////")
        getAction(this.url.dataList).then(res => {

          this.scheduleType=[]
          if (res.success) {
            console.log("-------------------------")
            this.scheduleType = res.result.records
          }
        })

        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.scheduleId=this.model.scheduleId;
        this.viewer=this.model.viewer;
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'dutyLog','title'))
		  //时间格式化
          this.form.setFieldsValue({dutyDate:this.model.dutyDate?moment(this.model.dutyDate):null})
        });


      },
      choseUser: function(userList) {
        this.selectedUser = []
        this.userIds = []
        this.viewer=''
        for (var i = 0; i < userList.length; i++) {
          this.selectedUser.push(userList[i].name)
          this.userIds.push(userList[i].id)
          this.viewer += userList[i].userId + ','
        }
      },
      close () {
        this.selectedUser = []
        this.viewer=''
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
            formData.scheduleId = this.scheduleId
            console.log("------------------------",this.userIds)

            formData.viewer=this.viewer;
            //时间格式化
            formData.dutyDate = formData.dutyDate?formData.dutyDate.format('YYYY-MM-DD'):null;
            
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
      getData: function() {
        let id = sessionStorage.getItem('id')
        if (id !== '') {
          getAction(this.url.queryDealList, { id: id }).then(res => {
            if (res.success) {
              let data = res.result
              if (typeof data == null) {
                this.selectedUser = []
              } else {
                for (var i = 0; i < data.length; i++) {
                  this.selectedUser.push(data[i].realname)
                  this.userIds.push(data[i].id)
                }
                console.log("userId",this.userIds)
              }
              console.log(this.selectedUser)
            }
          })
        } else {
          return
        }
      },
      modalFormOk(userList) {
        this.selectedUser = userList.selectedUser
        this.userIds = userList.userId
        this.getData()
      },
      // 选择员工
      onSearch() {
        this.$refs.staffList.open()
      },
      open(record) {
        this.edit(record)
        getAction(this.url.queryDealList, { id: record.viewer }).then(res => {
          if (res.success) {
            let data = res.result
            if (typeof data == null) {
              this.selectedUser = []
            } else {
              for (var i = 0; i < data.length; i++) {
                this.selectedUser.push(data[i].realname)
                this.userIds.push(data[i].id)
              }
            }
          }
        })
      }


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