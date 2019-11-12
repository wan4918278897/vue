<template>
  <div class="page-header-index-wide">
    <a-row :gutter="12">
      <a-col :xl="12" :lg="24" :md="24" :sm="24" :xs="24">
        <div class="title">
          <p>事件信息</p>
         <router-link to="/evenReport/eventPublish"><span class="more">more...</span></router-link>
        </div>
        <staffList ref="staffList"></staffList>
      </a-col>
      <a-col :xl="12" :lg="24" :md="24" :sm="24" :xs="24">
        <div class="title">
          <p>工作动态</p>
          <router-link to="/workDynamic"><span class="more">more...</span></router-link>
        </div>
        <workTrend ref="workTrend"></workTrend>
      </a-col>
    </a-row>
     <a-row :gutter="12" style="margin-top:20px;">
      <a-col :xl="12" :lg="24" :md="24" :sm="24" :xs="24">
        <div class="title">
          <p>典型案例</p>
          <router-link to="/briefing"><span class="more">more...</span></router-link>
        </div>
        <typicalCase ref="typicalCase"></typicalCase>
      </a-col>
      <a-col :xl="12" :lg="24" :md="24" :sm="24" :xs="24">
        <div class="title">
          <p>国内动态</p>
          <router-link to="/countryDynamic"><span class="more">more...</span></router-link>
        </div>
        <workTrend ref="workTrend"></workTrend>
      </a-col>
    </a-row>
  </div>
</template>

<script>
import { JeecgListMixin } from '@/mixins/JeecgListMixin'
import { FullCalendar } from 'vue-full-calendar'
import { getAction, httpAction, deleteAction } from '@/api/manage'
import staffList from './eventInformation' //事件信息
import workTrend from './workTrend' //工作动态
import typicalCase from './typicalCase' //典型案例

export default {
  name: 'Analysis',
  components: {
    staffList,
    workTrend,
    typicalCase
  },
  data() {
    return {
      loading: true,
      center: null
    }
  },
  created() {
    // this.loadData();
  },
  methods: {}
}
</script>

<style lang="scss" scoped>
.title {
  height: 50px;
  line-height: 50px;
  font-size: 16px;
  color: rgba(0, 0, 0, 0.85);
  font-weight: 500;
  width: 100%;
  background: #fff;
  padding-left: 20px;
  p {
    float: left;
    padding:0;
    margin:0;
  }
  .more {
    font-size: 14px;
    color: #1890ff;
    float: right;
    padding-right: 20px;
    cursor: pointer;
  }
}
</style>