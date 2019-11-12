<template>
  <div class="wrapper">
    <fullcalendar></fullcalendar>
  </div>
</template>

<script>
  import fullcalendar from '../../components/fullcalendar/fullcalendar'
  // import { JeecgListMixin } from '@/mixins/JeecgListMixin'

  export default {
    name: "ScheduleCalender",
    components: {
      fullcalendar
    },
    data () {
      return {
        
    }
  },
  computed: {
    importExcelUrl: function(){
      return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
    }
  },
    methods: {
       
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less'
</style>