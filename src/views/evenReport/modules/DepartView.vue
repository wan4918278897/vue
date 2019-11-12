<!--选择部门弹窗-->
<template>
  <a-modal
    :width="modalWidth"
    :visible="visible"
    title="部门搜索"
    :confirmLoading="confirmLoading"
    :maskClosable="false"
    @ok="handleSubmit"
    @cancel="handleCancel"
    cancelText="关闭"
    wrapClassName="ant-modal-cust-warp"
  >
    <a-row :gutter="10">
      <a-col :md="7" :sm="10">
        <a-card :bordered="false">
          <div style="background: #fff;padding-left:12px;height: 100%; margin-top: 5px">
            <a-input-search @search="onSearch" style="width:100%;margin-top: 5px" placeholder="请输入部门名称"/>

            <!--部门树-->
            <template>
              <!--组织机构-->
              <a-directory-tree
                @select="onSelect"
                :selectedKeys="selectedKeys"
                :checkedKeys="checkedKeys"
                :treeData="departTree"
                :checkStrictly="true"
                :expandedKeys="iExpandedKeys"
                :autoExpandParent="autoExpandParent"
                @expand="onExpand"/>
            </template>


          </div>
        </a-card>
      </a-col>
      <a-col :md="17" :sm="16">
        <a-card :bordered="true">
          <a-tabs defaultActiveKey="1" @change="callback">
            <a-tab-pane tab="用户信息" key="1">
              <QueryUserListModal ref="QueryUserListModal" @choseUser="choseUser"></QueryUserListModal>
            </a-tab-pane>
          </a-tabs>
        </a-card>
      </a-col>
    </a-row>
  </a-modal>
</template>

<script>
  import pick from 'lodash.pick'
  import {getAction} from '@/api/manage'
  import {queryIdTree} from '@/api/api'
  import QueryUserListModal from "./QueryUserListModal";

  export default {
    name: "DepartView",
    components: {
      QueryUserListModal
    },
    data() {
      return {
        checkedKeys: [], // 存储选中的部门id
        userId: "", // 存储用户id字符串
        model: {}, // 存储SysUserDepartsVO表
        departList: [], // 存储部门信息
        modalWidth: 1100,
        departTree: [],
        selectedKeys: [],
        iExpandedKeys: [],
        autoExpandParent: true,
        title: "操作",
        visible: false,
        labelCol: {
          xs: {span: 24},
          sm: {span: 5}
        },
        wrapperCol: {
          xs: {span: 24},
          sm: {span: 16}
        },
        confirmLoading: false,
        headers: {},
        form: this.$form.createForm(this),
      }
    },
    methods: {
      callback(key) {
        console.log(key)
      },
      add() {
        this.edit({});
      },
      edit(record) {
        this.departList = [];
        this.queryDepartTree();
        this.form.resetFields();
        this.visible = true;
        this.model = Object.assign({}, record);
        let filedsVal = pick(this.model, 'id', 'userId', 'departIdList');
        this.$nextTick(() => {
          this.form.setFieldsValue(filedsVal);
        });
      },
      tipDetail(selectUser,userIds){
        this.userId = userIds;
      },
      close() {
        this.$emit('close');
        this.visible = false;
        this.departList = [];
        this.checkedKeys = [];
        this.$refs.QueryUserListModal.handleCancel();
      },
      handleSubmit() {
        console.log(123)
        const that = this;
        // 触发表单验证
          let formData = {
            userId: this.userId,
            selectedUser: this.selectedUser
          };
          that.$emit('ok', formData);
          that.close();

      },
      handleCancel() {
        this.close();
      },
      onSelect(selectedKeys, e) {
        if (this.selectedKeys[0] !== selectedKeys[0]) {
          this.selectedKeys = [selectedKeys[0]];
        }
        let record = e.node.dataRef;
        this.checkedKeys.push(record.id);
        this.$refs.QueryUserListModal.open(record);
      },
      onSearch(value) {
        let that = this
        if (value) {
          searchByKeywords({keyWord: value}).then((res) => {
            if (res.success) {
              that.departTree = []
              for (let i = 0; i < res.result.length; i++) {
                let temp = res.result[i]
                that.departTree.push(temp)
              }
            } else {
              that.$message.warning(res.message)
            }
          })
        } else {
          that.loadTree()
        }

      },
      onCheckAll(o) {
        if (this.checkStrictly) {
          this.checkedKeys = o.checked;
        } else {
          this.checkedKeys = o
        }
      },
      queryDepartTree() {
        queryIdTree().then((res) => {
          if (res.success) {
            this.departTree = res.result;
          }
        })
      },

      onExpand(expandedKeys) {
        this.iExpandedKeys = expandedKeys
        this.autoExpandParent = false
      },
      choseUser: function (userList) {
        this.selectedUser = [];
        this.userId = [];
        for (var i = 0; i < userList.length; i++) {
          this.selectedUser.push(userList[i].realname);
          this.userId += userList[i].id + ","
        }

      }
    },
  }
</script>

<style  scoped>
  .ant-table-tbody .ant-table-row td {
    padding-top: 10px;
    padding-bottom: 10px;
  }
  .ant-modal-cust-warp .ant-modal-body{
      height:662px!important;
      background: #ccc;
  }
  .ant-form-item {
    margin-bottom: 0;
  }
  
</style>