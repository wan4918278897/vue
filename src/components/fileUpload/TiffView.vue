<template>
  <a-modal
    :width="1000"
    ,
    :visible="visible"
    :confirmLoading="confirmLoading"
    @cancel="handleCancel"
    title="传真文件预览"
    :footer="null"
  >
    <a-carousel arrows :dots="false">
      <div
        slot="prevArrow"
        slot-scope="props"
        class="custom-slick-arrow"
        style="left: 10px;zIndex: 1"
      >
        <a-icon type="left-circle" />
      </div>
      <div slot="nextArrow" slot-scope="props" class="custom-slick-arrow" style="right: 10px">
        <a-icon type="right-circle" />
      </div>
      <div v-for="(text, index) in imgList" :key="index">
        <img :src="getAvatarView(text)" style="width: 95%" />
      </div>
    </a-carousel>
  </a-modal>
</template>

<script>
import { ACCESS_TOKEN } from '@/store/mutation-types'
import { getAction, postAction, deleteAction } from '@/api/manage'

export default {
  name: 'TiffView',
  modalWidth: 1000,
  data() {
    return {
      visible: false,
      confirmLoading: false,
      imgList: [],
      url: {
        list:'',
        translateTiff: '/sys/file/viewTiff',
        imgerver: window._CONFIG['domianURL'] + '/sys/file/view/files/'
      }
    }
  },

  // http://localhost:8080/jeecg-boot/sys/common/view/user/20190401/20180607175028Fn1Lq7zw_1554118444672.png
  methods: {
    getAvatarView: function(text) {
      return this.url.imgerver + '/' + text
    },

    open(path, viewPath) {
      let param = {
        filePath: path,
        viewPath: viewPath
      }
      getAction(this.url.translateTiff, param).then(res => {
        this.imgList = res
      })
      this.visible = true
    },
    close() {
      this.$emit('close')
      this.visible = false
    },
    handleCancel() {
      this.close()
    }
  }
}
</script>

<style scoped>

.ant-carousel .slick-prev,
.ant-carousel .slick-next {
  background: #0000009c;
  color: #000000bd;
  border-radius: 50%;
}
.ant-carousel >>> .slick-slide {
  text-align: center;
  overflow: hidden;
}
.ant-carousel >>> .custom-slick-arrow {
  width: 40px;
  height: 40px;
  font-size: 40px;
  color: #fff;
  background-color: rgba(0,0,0,0.5);
}
.ant-carousel >>> .custom-slick-arrow:before {
  display: none;
}
.ant-carousel >>> .custom-slick-arrow:hover {
  opacity: 0.5;
}
.ant-carousel >>> .slick-slide h3 {
  color: #fff;
}
</style>