<template>
  <a-row :gutter="10">
    <a-col :md="6" :sm="24">
      <a-card :bordered="false">
        <div style="background: #fff;padding-left:16px;height: 100%; margin-top: 5px">
          <a-input-search @search="onSearch" style="width:100%;margin-top: 10px" placeholder="请输入部门名称"/>
          <!-- 树-->
          <template>

            <!--组织机构-->
            <a-directory-tree
              @select="onSelect"
              @check="onCheck"
              :selectedKeys="selectedKeys"
              :checkedKeys="checkedKeys"
              :treeData="departTree"
              :checkStrictly="true"
              :expandedKeys="iExpandedKeys"
              :autoExpandParent="autoExpandParent"
              @expand="onExpand"
            />
          </template>
        </div>
      </a-card>
    </a-col>
    <a-col :md="18" :sm="24">
      <a-card :bordered="false">
        <a-tabs defaultActiveKey="1" @change="callback">
          <a-tab-pane tab="用户信息" key="1">
          <Contacts-List ref="DeptUserInfo"></Contacts-List>
        </a-tab-pane>

        </a-tabs>
      </a-card>
    </a-col>
  </a-row>
</template>
<script>
  import ContactsList from './ContactsList'
  import {queryDepartTreeList, searchByKeywords} from '@/api/api'
  import {JeecgListMixin} from '@/mixins/JeecgListMixin'
    
/*  import forEach from 'lib.dom.d.ts'*/

  export default {
    name: 'ContactsBookList',
    mixins: [JeecgListMixin],
    components: {
      ContactsList,
    },
    data() {
      return {
        toTable:'',
        currentDeptId: '',
        iExpandedKeys: [],
        loading: false,
        autoExpandParent: true,
        currFlowId: '',
        currFlowName: '',
        disable: true,
        treeData: [],
        visible: false,
        departTree: [],
        rightClickSelectedKey: '',
        hiding: true,
        model: {},
        dropTrigger: '',
        depart: {},
        disableSubmit: false,
        checkedKeys: [],
        selectedKeys: [],
        autoIncr: 1,
        currSelected: {},
        form: this.$form.createForm(this),
        labelCol: {
          xs: {span: 24},
          sm: {span: 5}
        },
        wrapperCol: {
          xs: {span: 24},
          sm: {span: 16}
        },
        graphDatasource: {
          nodes: [],
          edges: []
        },
      }
    },
    methods: {
      callback(key) {
        console.log(key)
      },
      loadData() {
        this.refresh();
      },
      loadTree() {
        var that = this
        that.treeData = []
        that.departTree = []
        queryDepartTreeList().then((res) => {
          if (res.success) {
            for (let i = 0; i < res.result.length; i++) {
              let temp = res.result[i]
              that.treeData.push(temp)
              that.departTree.push(temp)
              that.setThisExpandedKeys(temp)
            }
            this.loading = false
          }
        })
      },
      setThisExpandedKeys(node) {
        if (node.children && node.children.length > 0) {
          this.iExpandedKeys.push(node.key)
          for (let a = 0; a < node.children.length; a++) {
            this.setThisExpandedKeys(node.children[a])
          }
        }
      },
      refresh() {
        this.loading = true
        this.loadTree()
      },

      onExpand(expandedKeys) {
        this.iExpandedKeys = expandedKeys
        this.autoExpandParent = false
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
      onCheck(checkedKeys, e) {
        let record = e.node.dataRef;
        // console.log('onCheck', checkedKeys, e);
        this.checkedKeys = [];
        // if (e.checked === true) {
        this.currentDeptId = record.id;
        this.checkedKeys.push(record.id);
        this.$refs.DeptUserInfo.open(record);


        this.hiding = false;
        // this.checkedKeys = checkedKeys.checked
      },
      setValuesToForm(record)
      {
        /*/!*this.form.getFieldDecorator('fax', {initialValue: ''})*!/
        this.form.setFieldsValue(pick(record, 'departName', 'orgCode', 'departOrder', 'mobile', 'fax', 'address', 'memo'))*/
      },
      searchTable(record)
      {
        console.log("recorddezhi:")
        console.log(record);
      },
      onSelect(selectedKeys, e) {
        if (this.selectedKeys[0] !== selectedKeys[0]) {
          this.selectedKeys = [selectedKeys[0]];
        }
        let record = e.node.dataRef;
        this.checkedKeys.push(record.id);
        this.$refs.DeptUserInfo.onClearSelected();
        this.$refs.DeptUserInfo.open(record);
      },
    },
    url: {
      list: "/emergencyduty/contacts/list",
      delete: "/emergencyduty/contacts/delete",
      deleteBatch: "/emergencyduty/contacts/deleteBatch",
      exportXlsUrl: "emergencyduty/contacts/exportXls",
      importExcelUrl: "emergencyduty/contacts/importExcel",
    },
    created() {
      this.currFlowId = this.$route.params.id
      this.currFlowName = this.$route.params.name
      // this.loadTree()
    },
  }
</script>
<style scoped>
</style>