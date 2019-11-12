<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    :maskClosable="false"
    @cancel="handleCancel"
    cancelText="关闭">

    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="单位">
          <a-auto-complete
            :dataSource="indexDepartSource"
            placeholder="请输入单位名称"
            @search="indexDepart"
            v-decorator="['departName',  {rules: [{ required: true, message: '请输入地址' }]}]"
          />
        </a-form-item>

        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="地址">

          <a-select
            showSearch
            :value="value"
            placeholder="请输入地址"
            :defaultActiveFirstOption="false"
            style="width: 100%"
            :filterOption="false"
            @search="fetchUser"
            @change="handleChange"
            :notFoundContent="fetching ? undefined : null"
          >
            <a-spin v-if="fetching" slot="notFoundContent" size="small"/>
            <a-select-option
              v-for="(item,index) in indexAddressSource"
              :key="index"
              :value="item.id"
            >{{item.name}}
            </a-select-option>

          </a-select>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="校区">
          <a-select
            placeholder="请选择校区"
            v-decorator="['area', {rules: [{ required: true, message: '选择校区' }]}
          ]">
            <a-select-option value="主校区">主校区</a-select-option>
            <a-select-option value="东校区">东校区</a-select-option>
            <a-select-option value="同济校区">同济校区</a-select-option>
          </a-select>
        </a-form-item>
        <a-row :gutter="12">
          <a-col :md="10" :sm="10" :offset="3">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="经度">
              <a-input v-decorator="['longitude', {}]" disabled/>
            </a-form-item>
          </a-col>
          <a-col :md="9" :sm="10">
            <a-form-item
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
              label="纬度">
              <a-input v-decorator="['latitude', {}]" disabled/>
            </a-form-item>
          </a-col>
        </a-row>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="房间">
          <a-input placeholder="请输入房间" v-decorator="['room', {}]"/>
        </a-form-item>
        <a-form-item
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          label="备注">
          <a-input placeholder="请输入备注" v-decorator="['remark', {}]"/>
        </a-form-item>


      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
  import {httpAction, getAction} from '@/api/manage'
  import pick from 'lodash.pick'
  import debounce from 'lodash/debounce';


  export default {
    name: "TestDemoModal",

    data() {
      this.lastFetchId = 0;
      this.fetchUser = debounce(this.fetchUser, 800);
      return {

        title: "操作",
        visible: false,
        indexNameSource: [],
        indexDepartSource: [],
        indexAddressSource: [],
        data: [],
        value: [],
        fetching: false,
        value: undefined,
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
          add: "/testDemo/testDemo/add",
          edit: "/testDemo/testDemo/edit",
          indexDepartList: '/testDemo/testDemo/indexDepartSource',
          indexAddressSource: '/testDemo/testDemo/indexAddressSource'

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
          this.form.setFieldsValue(pick(this.model, 'address', 'area', 'latitude', 'longitude', 'remark', 'room', 'departName'))
          //时间格式化
        });

      },
      close() {
        this.$emit('close');
        this.visible = false;
        this.indexDepartSource = [];
        this.indexAddressSource = [];
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

      indexDepart(value) {
        let that = this;
        that.indexDepartSource = [];
        let param = {
          departName: value
        }
        if (that.indexNameSource.length == 0) {
          getAction(that.url.indexDepartList, param).then((res) => {
            for (var i = 0; i < res.length; i++) {
              that.indexDepartSource.push(res[i])
            }
          })
        }
      },
      indexAddress() {
        let that = this;
        that.indexAddressSource = [];
        getAction(that.url.indexAddressSource).then((res) => {
          that.indexAddressSource = res;
          console.log(that.indexAddressSource)
        })
      },
      fetchUser(value) {
        console.log('fetching user', value);
        this.lastFetchId += 1;
        const fetchId = this.lastFetchId;
        this.data = []
        this.fetching = true
        if (fetchId !== this.lastFetchId) { // for fetch callback order
          return;
        }
        let param={
          s:value
        }
        getAction(this.url.indexAddressSource,param).then((res) => {
          if (res.success) {
            this.indexAddressSource = res.result;
          }
          this.fetching = false

       })
      },
      handleChange(value) {
        Object.assign(this, {
          value,
          data: [],
          fetching: false,
        })
      }

    }
  }
</script>

<style lang="less" scoped>

</style>