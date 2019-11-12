<template>
  <div class="mapWrapper" :style="height">
    <div id="container"></div>
    <div class="info">
      <h4 id="status"></h4>
      <hr/>
      <p id="result"></p>
      <hr/>
      <p>由于众多浏览器已不再支持非安全域的定位请求，为保位成功率和精度，请升级您的站点到HTTPS。</p>
    </div>
    <div class="imgList">
      <!-- 右侧弹框 -->
      <a-drawer
        title="可标记列表"
        placement="right"
        :closable="false"
        @close="onClose"
        :visible="visible"
        :width="550"
      >
        <div class="table-page-search-wrapper">
          <a-form layout="inline">
            <a-row :gutter="6">
              <a-col :md="8" :sm="10">
                <a-form-item label="单位">
                  <a-auto-complete
                    :dataSource="indexDepartSource"
                    placeholder="请输入单位名称"
                    @search="indexDepart"
                    v-model="queryParam.departName"
                  />
                </a-form-item>
              </a-col>
              <a-col :md="8" :sm="10">
                <a-form-item label="地址">
                  <a-auto-complete
                    :dataSource="indexAddressSource"
                    placeholder="请输入地址"
                    @select="onSelect"
                    @search="indexAddress"
                    v-model="queryParam.address"
                  />
                  <!--<a-input placeholder="请输入名称" v-model="queryParam.name"></a-input>-->
                </a-form-item>
              </a-col>

              <a-col :md="8" :sm="10">
                <a-form-item label="校区">
                  <a-select v-model="queryParam.area" placeholder="请选择校区">
                    <!--<a-select-option value="">请选择发布状态</a-select-option>-->
                    <a-select-option value="主校区">主校区</a-select-option>
                    <a-select-option value="东校区">东校区</a-select-option>
                    <a-select-option value="同济校区">同济校区</a-select-option>
                  </a-select>
                </a-form-item>
              </a-col>
              <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">

              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 4px">重置</a-button>
              </span>
            </a-row>
          </a-form>
        </div>

        <div class="userInformationTable">
          <a-table
            ref="table"
            size="middle"
            bordered
            rowKey="id"
            :columns="columns"
            :dataSource="dataSource"
            :pagination="ipagination"
            :loading="loading"
            :customRow="mark"
            :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
            @change="handleTableChange"
          ></a-table>
        </div>
      </a-drawer>
    </div>
    <!-- 圈选 -->
    <a-button-group>
      <a-button @click="huayuan()">圈选</a-button>
      <a-button type="primary" @click="showDrawer">标记</a-button>
    </a-button-group>
  </div>
</template>
<script>
  import {JeecgListMixin} from '@/mixins/JeecgListMixin'
  import {filterObj} from '@/utils/util'
  import {deleteAction, getAction, downFile, httpAction, postAction} from '@/api/manage'

  export default {
    mixins: [JeecgListMixin],
    name: 'MapTestList',
    // name: 'WebSocket',
    data() {
      return {
        positions: {
          lng: '',
          lat: '',
          Address: '未定位',
          citycode: '',
          loaded: false
        },
        map: '',
        relateId: '',
        type: '',
        detailedAddress: '山东青岛李沧区李村社区',
        markLng: '',
        markLat: '',
        targetValue: '',
        visible: false,
        isCircleSelectInfo: false,
        marker: [],
        indexAddressSource: [],
        indexDepartSource: [],
        //表头
        columns: [

          {
            title: '单位',
            align: 'center',
            dataIndex: 'departName'
          },
          {
            title: '地址',
            align: 'center',
            dataIndex: 'address'
          },
          {
            title: '房间',
            align: 'center',
            dataIndex: 'room'
          },
        ],

        height: {
          height: '',
        },
        url: {
          list: "/testDemo/testDemo/list",
          remark: '/testDemo/testDemo/remark',
          queryById: '/testDemo/testDemo/queryById',
          locate: '/emergencySource/emergencySource/getDataSource',
          remarkList: '/testDemo/testDemo/remarkList',
          indexAddressSource: '/testDemo/testDemo/indexAddressSource',
          indexDepartList: '/testDemo/testDemo/indexDepartSource'

        }
      }
    },
    created() {
      this.getClientHeight()
    },
    mounted() {
      this.location()
    },
    methods: {
      //初始化地图
      init(x, y, zoom) {
        let that = this
        that.map = new AMap.Map('container', {
          center: [x, y],
          resizeEnable: true,
          zoom: zoom
        })
      },
      //获取屏幕高度
      getClientHeight() {
        this.height.height = `${document.documentElement.clientHeight}` - 130 + 'px';
      },
      //定位
      location() {
        let that = this
        const map = new AMap.Map('container')
        AMap.plugin('AMap.Geolocation', function () {
          var geolocation = new AMap.Geolocation({
            enableHighAccuracy: true, //是否使用高精度定位，默认:true
            timeout: 10000, //超过10秒后停止定位，默认：5s
            buttonPosition: 'RB', //定位按钮的停靠位置
            buttonOffset: new AMap.Pixel(10, 20), //定位按钮与设置的停靠位置的偏移量，默认：Pixel(10, 20)
            zoomToAccuracy: true //定位成功后是否自动调整地图视野到定位点
          })
          map.addControl(geolocation)
          geolocation.getCurrentPosition(function (status, result) {
            if (result.status) {
              that.lat = result.position.lat
              that.lng = result.position.lng
              that.positions.lat = result.position.lat;
              that.positions.lng = result.position.lng;
              that.init(result.position.lng, result.position.lat, 18);
            }
          })
        })
      },
      //根据地址获取经纬度
      getIntandlat(record) {
        let that = this
        let data = {
          key: '389880a06e3f893ea46036f030c94700',
          url: 'http://restapi.amap.com/v3/geocode/geo',
          address: '湖北省武汉市华中科技大学' + record.address
        }
        getAction(that.url.locate, data).then(res => {
          console.log(res)
          if (res.status) {
            let s = res.geocodes[0].location.split(",");
            let lng = s[0];
            let lat = s[1];
            this.init(lng, lat, 18)
            this.testevent(record.id, record.type)
          }
        })
      },
      //给地图绑定点击事件并添加标记
      testevent(id, type) {
        let that = this
        // var map = new AMap.Map('container') //重新new出一个对象，传入参数是div的id
        AMap.event.addListenerOnce(that.map, 'click', function (e) {
          //添加点击事件,传入对象名，事件名，回调函数
          that.map.remove(that.marker);
          this.markLng = e.lnglat.getLng()
          //维度
          this.markLat = e.lnglat.getLat()
          let param = {
            id: id,
            lat: this.markLat,
            lng: this.markLng,
            type: that.type
          }
          postAction(that.url.remark, param).then((res) => {
            if (res.success) {
              that.$message.success(res.message);
            } else {
              that.$message.warning(res.message);
            }
          })

          var marke = new AMap.Marker({
            position: new AMap.LngLat(this.markLng, this.markLat),
            offset: new AMap.Pixel(-13, -30)
          })
          that.map.add(marke)
          that.marker.push(marke)
        })

      },
      //圈选
      huayuan() {
        let that = this
        that.targetValue = ''
        var mouseTool = new AMap.MouseTool(this.map)
        //监听draw事件可获取画好的覆盖物
        var overlays = []
        let circle = [];
        mouseTool.on('draw', function (e) {
          that.map.remove(that.marker)          //精度
          that.marker = [];
          overlays.push(e.obj)
          circle = new AMap.Circle({
            center: e.obj.getCenter(),
            radius: e.obj.getRadius(), //半径,
          })
          // that.map.add(circle)
          // circle.setMap(hat.map)
          let param = {
            type: ''
          }
          getAction(that.url.remarkList).then(res => {
            for (var i = 0; i < res.length; i++) {
              var p = [res[i].longitude, res[i].latitude]
              if (circle.contains(p)) {
                let m = that.renderingMarker(res[i].id, res[i].longitude, res[i].latitude);
                that.marker.push(m)
              }
            }
          })
        })
        mouseTool.circle({
          fillColor: '#00b0ff',
          strokeColor: '#80d8ff'
          //同Circle的Option设置
        })
        //鼠标抬起清除当前圆
        AMap.event.addListener(that.map, 'mouseup', function (e) {
          // that.map.remove(that.overlays)
          that.map.remove(overlays)
          that.map.remove(that.marker);
          that.map.remove(circle);

          mouseTool.close(true)
          that.isCircleSelectInfo = false
        })
      },
      //渲染标记
      renderingMarker(id, lng, lat) {
        let that = this;
        // 将 Icon 实例添加到 marker 上:
        var marker = new AMap.Marker({
          position: new AMap.LngLat(lng, lat),
          offset: new AMap.Pixel(-13, -30),
          map: that.map
        })
        marker.setMap(that.map)
        //给标注添加信息窗体
        var infoWindow = new AMap.InfoWindow({offset: new AMap.Pixel(0, -30)})
        marker.on('click', function (e) {
          let param = {
            id: id
          }
          getAction(that.url.queryById, param).then((res) => {
            if (res.success) {
              let record = res.result;
              let id = record.id;
              marker.setLabel({
                offset: new AMap.Pixel(20, 20),  //设置文本标注偏移量
                content: "<table >" +
                "<tr><td>单位：</td><td>" + record.departName + "</td></tr>" +
                "<tr><td>地址：</td><td>" + record.address + "</td></tr>" +
                "<tr><td>校区：</td><td>" + record.area + "</td></tr>" +
                "<tr><td>备注：</td><td>" + record.remark + "</td></tr>" +
                "</table>", //设置文本标注内容
                direction: 'top',//设置文本标注方位
              });
            }
          })
        })

        return marker
        //根据标注自动缩放地图到合适的视野级别
        // that.map.setFitView()
      },
      showDrawer() {
        this.loadData()
        this.visible = true
      },
      onClose() {
        this.visible = false;
      },
      mark(record) {
        let that = this;
        return {
          on: {
            click: () => {
              that.selectedRowKeys = [];
              that.selectedRowKeys.push(record.id)
              if (typeof record.latitude == 'undefined' && typeof record.longitude == 'undefined') {
                that.getIntandlat(record);
              } else {
                that.init(record.longitude, record.latitude, 18)
                let m = that.renderingMarker(record.id, record.longitude, record.latitude)
                that.marker.push(m)
                that.testevent(record.id, record.type)
              }
              that.relateId = record.relatedId;
              that.type = record.type;
            }
          }
        }
      },
      indexAddress(value) {
        let that = this;
        that.indexAddressSource = [];
        let param = {
          address: value
        }
        if (that.indexAddressSource.length == 0) {
          getAction(that.url.indexAddressSource, param).then((res) => {
            for (var i = 0; i < res.length; i++) {
              that.indexAddressSource.push(res[i])
            }
          })
        }
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
      onSelect(value) {
        console.log('onSelect', value);
      }
    }
  }
</script>

<style scoped lang="less">
  html,
  body,
  #container {
    height: 100%;
  }

  .mapWrapper {
    position: relative;
  }

  .info {
    width: 20rem;
  }

  .imgList {
    position: absolute;
    bottom: 0;
    left: 0;
    height: 80px;
    background: #fff;
    border: 1px solid #666;
    z-index: 999;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    overflow: hidden;
    .resource {
      width: 80px;
      text-align: center;
      background: #2f7fee;
      color: #fff;
      cursor: pointer;
      p {
        margin-bottom: 0;
        img {
          width: 26px;
          height: 26px;
          margin-top: 14px;
        }
      }
    }
    .ant-radio-group {
      .ant-radio--wrapper {
        height: 70px;
        margin-top: 5px;
        box-sizing: border-box;
        position: relative;
      }
      .ant-radio {
        position: absolute;
        top: 12px;
      }
    }
    .sign {
      height: 70px;
      width: 50px;
      border: 1px solid #f3f3f3;
      border-radius: 10px;
      height: 56px;
      text-align: center;
      img {
        width: 40px;
      }
      p {
        font-size: 12px;
      }
    }
  }

  // 圈选
  .ant-btn-group {
    position: absolute;
    top: 0;
    left: 2%;
  }

  //圈选内容
  .circleSelect {
    position: absolute;
    top: 32px;
    left: 2%;
    padding: 10px;
    background: rgba(0, 0, 0, 0.5);
    .circleSelect_info {
      tr {
        list-style-type: none;
        color: #fff;
        font-size: 14px;
        text-align: left;
      }
    }
  }

  //信息窗口样式
  .content-window-card {
    position: relative;
    box-shadow: none;
    bottom: 0;
    left: 0;
    width: auto;
    padding: 0;
  }

  .content-window-card p {
    height: 2rem;
  }

  .custom-info {
    border: solid 1px silver;
  }

  div.info-top {
    position: relative;
    background: none repeat scroll 0 0 #f9f9f9;
    border-bottom: 1px solid #ccc;
    border-radius: 5px 5px 0 0;
  }

  div.info-top div {
    display: inline-block;
    color: #333333;
    font-size: 14px;
    font-weight: bold;
    line-height: 31px;
    padding: 0 10px;
  }

  div.info-top img {
    position: absolute;
    top: 10px;
    right: 10px;
    transition-duration: 0.25s;
  }

  div.info-top img:hover {
    box-shadow: 0px 0px 5px #000;
  }

  div.info-middle {
    font-size: 12px;
    padding: 10px 6px;
    line-height: 20px;
  }

  div.info-bottom {
    height: 0px;
    width: 100%;
    clear: both;
    text-align: center;
  }

  div.info-bottom img {
    position: relative;
    z-index: 104;
  }

  span {
    margin-left: 5px;
    font-size: 11px;
  }

  .info-middle img {
    float: left;
    margin-right: 6px;
  }

  .amap-marker-label {
    border: 0;
    background-color: transparent;
  }

  .info {
    position: relative;
    top: 0;
    right: 0;
    min-width: 0;
  }

  #yzx {

  }
</style>