<template>
  <div class="wrapper">
    <a-upload
      name="file"
      :showUploadList="false"
      :multiple="false"
      :headers="tokenHeader"
      :action="upDateFile"
      @change="fileUpload"
    >
      <a-button type="primary" class="uploadingStyle">
        <a-icon type="upload"/>
        文件上传
      </a-button>
    </a-upload>
    <!-- 附件列表 -->
    <div class="tableList">
      <a-table
        ref="table"
        size="small"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
      >
        <span slot="action" slot-scope="text, record">
          <a href="javascript:;" @click="fileView(record)">预览</a>
          <pdf-preview-modal ref="pdfmodal"></pdf-preview-modal>
          <!-- table区域-end -->
          <tiff-view ref="tiffView"></tiff-view>
          <a-divider type="vertical"/>
          <a-dropdown>
            <a class="ant-dropdown-link" @click="uploadFile(record.viewPath)">下载</a>
          </a-dropdown>
          <a-divider type="vertical"/>
          <a-dropdown>
            <a class="ant-dropdown-link" @click="deleteHandle(record.id)">删除</a>
          </a-dropdown>
        </span>
      </a-table>
    </div>


  </div>

</template>
<script>
  import Vue from 'vue'
  import {httpAction, getAction, postAction, deleteAction} from '@/api/manage'
  import PdfPreviewModal from '@/views/jeecg/modules/PdfPreviewModal'
  import {ACCESS_TOKEN} from '@/store/mutation-types'
  import TiffView from './TiffView.vue'
  import {notification} from 'ant-design-vue'

  export default {
    name: 'FileUpload',
    components: {
      PdfPreviewModal,
      TiffView
    },
    props: ['relateType', 'relateId'],//relateType文件类型
    data() {
      return {
        tokenHeader: {'X-Access-Token': Vue.ls.get(ACCESS_TOKEN)},
        dataSource:[],
        text: '',
        url: {
          list: '/fileManage/fileManage/list',
          delete: '/fileManage/fileManage/delete',
          upDateFile: 'sys/file/upload',
          pdfView: 'sys/file/pdf/pdfPreviewIframe',
          downLoad: 'sys/file/download',
          upDateRelateId: 'sys/file/upDateRelateId',

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
        ]
      }
    },
    computed: {
      //文件上传
      upDateFile: function () {
        return `${window._CONFIG['domianURL']}/${this.url.upDateFile + '?relateType=' + this.relateType + '&' + 'relateId=' + this.relateId}`
      }
    },
    created() {
      this.loadData();
      //初始化字典配置 在自己页面定义
    },
    methods: {

      loadData(arg) {
        //加载数据 若传入参数1则加载第一页的内容
        var params = [];//查询条件
        params.relateId = this.relateId;
        params.relateType = this.relateType;
        console.log(params)
        //undefined
        getAction(this.url.list, params).then(res => {
          if (res.success) {
            this.dataSource = res.result.records
          }
        })
      },
      //文件预览方法
      fileView(record) {
        let fileType = record.fileType;
        if (fileType === "pdf") {
          this.pdfPreview(record.fileName, record.viewPath)
        } else if (fileType === "tif" || fileType === "tiff") {
          this.$refs.tiffView.open(record.path, record.viewPath);

        } else if (fileType === "png" || fileType === "bmp" || fileType === "jpg" || fileType === "jpeg") {
          this.getImgView(record.viewPath);
        } else {
          notification.error({message: '系统提示', description: '该类型文件不支持在线预览', duration: 1})
        }

      },

      fileUpload(info) {
        if (info.file.status === 'done') {
          if (info.file.response.success) {
            this.$message.success(`${info.file.name} 文件上传成功`)
            this.loadData()
          } else {
            this.$message.error(`${info.file.name} ${info.file.response.message}.`)
          }
        } else if (info.file.status === 'error') {
          this.$message.error(`文件上传失败: ${info.file.msg} `)
        }
      },
      //  pdf文件预览
      pdfPreview(name, path) {
        const token = Vue.ls.get(ACCESS_TOKEN);
        this.headers = {"X-Access-Token": token}
        this.$refs.pdfmodal.previewFiles(name, token, path);
      },
      downLoad(path) {
        getAction(this.url.downLoad, {path: path})
      },

      //s删除按钮方法
      deleteHandle(record) {
        let that = this;
        let param = {
          id: record
        };
        //依据关联的类型和Id删除无用附件
        deleteAction(that.url.delete, param).then((res) => {
          if (res.success) {
            that.loadData();
          } else {
            that.$message.warning(res.message)
          }
        })
      },
      /* 文件下载 */
      uploadFile(text) {
        if (!text) {
          this.$message.warning("未知的文件")
          return;
        }
        if (text.indexOf(",") > 0) {
          text = text.substring(0, text.indexOf(","))
        }
        console.log("111111111")
        console.log(text)
        window.open(window._CONFIG['domianURL'] + "/sys/file/download/files/" + text);


      },
    }
  }
</script>

<style lang="less">
  .uploadingStyle {
    margin-bottom: 16px;
  }

  :global {
    .tableList {
      .ant-table-small > .ant-table-content > .ant-table-body > table {
        table-layout: fixed !important;
      }
      .ant-table table {
        width: 100%;
        border-collapse: collapse;
        text-align: left;
        border-radius: 4px 4px 0 0;
        table-layout: fixed !important;
      }
      .ant-table-small > .ant-table-content > .ant-table-body > table > .ant-table-tbody > tr > td {
        width: 100px;
        overflow: hidden;
        text-overflow: ellipsis;
      }
    }
  }
</style>

