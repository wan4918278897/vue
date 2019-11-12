<template>
  <a-card :bordered="false">

    <!-- 查询区域 -->
    <!--<div class="table-page-search-wrapper">-->
    <!--<a-form layout="inline">-->
    <!--<a-row :gutter="24">-->

    <!--<a-col :md="6" :sm="8">-->
    <!--<a-form-item label="地址">-->
    <!--<a-input placeholder="请输入地址" v-model="queryParam.address"></a-input>-->
    <!--</a-form-item>-->
    <!--</a-col>-->
    <!--<a-col :md="6" :sm="8">-->
    <!--<a-form-item label="别名">-->
    <!--<a-input placeholder="请输入别名" v-model="queryParam.aliasName"></a-input>-->
    <!--</a-form-item>-->
    <!--</a-col>-->
    <!--<template v-if="toggleSearchStatus">-->
    <!--<a-col :md="6" :sm="8">-->
    <!--<a-form-item label="名称">-->
    <!--<a-input placeholder="请输入名称" v-model="queryParam.name"></a-input>-->
    <!--</a-form-item>-->
    <!--</a-col>-->
    <!--<a-col :md="6" :sm="8">-->
    <!--<a-form-item label="曾用名">-->
    <!--<a-input placeholder="请输入曾用名" v-model="queryParam.oldName"></a-input>-->
    <!--</a-form-item>-->
    <!--</a-col>-->
    <!--</template>-->
    <!--<a-col :md="6" :sm="8">-->
    <!--<span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">-->
    <!--<a-button type="primary" @click="searchQuery" icon="search">查询</a-button>-->
    <!--<a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>-->
    <!--<a @click="handleToggleSearch" style="margin-left: 8px">-->
    <!--{{ toggleSearchStatus ? '收起' : '展开' }}-->
    <!--<a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>-->
    <!--</a>-->
    <!--</span>-->
    <!--</a-col>-->

    <!--</a-row>-->
    <!--</a-form>-->
    <!--</div>-->


    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{
        selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        @change="handleTableChange">

        <span slot="action" slot-scope="text, record">
          <a href="javascript:;" @click="fileView(record)">预览</a>
           <a-divider type="vertical"/>
          <a-dropdown>
            <a class="ant-dropdown-link" @click="uploadFile(record.faxfile)">下载</a>
          </a-dropdown>
        </span>

      </a-table>
    </div>
    <!-- table区域-end -->
    <tiff-view ref="tiffView"></tiff-view>

  </a-card>
</template>

<script>
  import {JeecgListMixin} from '@/mixins/JeecgListMixin'
  import {getAction} from '@/api/manage'
  import TiffView from '../../components/fileUpload/TiffView'


  export default {
    name: "TiffManageList",
    mixins: [JeecgListMixin],
    components: {
      TiffView
    },
    data() {
      return {
        description: '标准库管理页面',
        viewPath:[],
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key: 'rowIndex',
            width: 60,
            align: "center",
            customRender: function (t, r, index) {
              return parseInt(index) + 1;
            }
          },
          {
            title: '名称',
            align: "center",
            dataIndex: 'title'
          },
          {
            title: '发送单位',
            align: "center",
            dataIndex: 'sender1'
          },
          {
            title: '时间',
            align: "center",
            dataIndex: 'recvtime'
          },

          {
            title: '操作',
            dataIndex: 'action',
            align: "center",
            scopedSlots: {customRender: 'action'},
          }
        ],
        url: {
          list: "/tiffManage/tiff/list",
          delete: "/testDemo/standardData/delete",
          deleteBatch: "/testDemo/standardData/deleteBatch",
          exportXlsUrl: "testDemo/standardData/exportXls",
          importExcelUrl: "testDemo/standardData/importExcel",
        },
      }
    },
    computed: {
      importExcelUrl: function () {
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      }
    },
    created() {
      this.loadData();
    },
    methods: {
      loadData() {
        var params = this.getQueryParams();//查询条件
        this.loading = true;

        console.log("param3:", params);
        getAction(this.url.list, params).then((res) => {
          console.log(res)
          if (res.success) {
            this.dataSource = res.result;
          }
          this.loading = false;
        })
      },
      //文件预览方法
      fileView(record) {
        this.viewPath=record.faxfile.split("files\\");
        this.$refs.tiffView.open(record.faxfile, this.viewPath[1]);
      },
      uploadFile(text) {
        let s=text.split("files\\")
        if (!s[1]) {
          this.$message.warning("未知的文件")
          return;
        }
        if (text.indexOf(",") > 0) {
          text = text.substring(0, text.indexOf(","))
        }
        window.open(window._CONFIG['domianURL'] + "/sys/file/downLoadFromUrl/files/" + s[1]);
      },
    }
  }
</script>
<style scoped>
</style>