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
      <div class="resource">
        <p>
          <img src="../../assets/right.png" alt/>
        </p>
        <p>资源分布</p>
      </div>
      <!-- //选项 -->
      <a-radio-group defaultValue="a" buttonStyle="solid" @change="onChange" v-model="targetValue">
        <a-radio :value="item.type" v-for="(item,index) in resource" :key="index">
          <div class="sign">
            <img :src="item.src" alt/>
            <p>{{item.name}}</p>
          </div>
        </a-radio>
      </a-radio-group>
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
            <a-row :gutter="18">
              <a-col :md="8" :sm="10">
                <a-form-item label="名称">
                  <a-input placeholder="请输入名称" v-model="queryParam.name"></a-input>
                </a-form-item>
              </a-col>
              <a-col :md="8" :sm="10">
                <a-form-item label="类型">
                  <a-select placeholder="请输入类型" v-model="queryParam.type">
                    <a-select-option value="A">危险源</a-select-option>
                    <a-select-option value="B">防护目标</a-select-option>
                    <a-select-option value="C">应急管理机构</a-select-option>
                    <a-select-option value="D">应急物资</a-select-option>
                    <a-select-option value="E">物资储备</a-select-option>
                    <a-select-option value="F">应急救援队伍</a-select-option>
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
    <!-- 圈选信息 -->
    <div class="circleSelect" v-show="isCircleSelectInfo">
      <div class="circleSelect_info">
        <tr>
          <td>危险源数目：</td>
          <td> {{emergencySource.A.length}}</td>
        </tr>
        <tr>
          <td>防护目标：</td>
          <td> {{emergencySource.B.length}}</td>
        </tr>
        <tr>
          <td>应急管理机构：</td>
          <td> {{emergencySource.C.length}}</td>
        </tr>
        <tr>
          <td>物资储备：</td>
          <td> {{emergencySource.D.length}}</td>
        </tr>
        <tr>
          <td>应急救援队伍：</td>
          <td> {{emergencySource.E.length}}</td>
        </tr>


      </div>
    </div>
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
        emergencySource: {
          A: [],
          B: [],
          C: [],
          D: [],
          E: []
        },
        resource: [
          {
            src: require('../../assets/wxy.png'),
            name: '危险源',
            type: 'A'
          },
          {
            src: require('../../assets/fhmb.png'),
            name: '防护目标',
            type: 'B'
          },
          {
            src: require('../../assets/yjjg.png'),
            name: '应急机构',
            type: 'C'
          },
          {
            src: require('../../assets/cbk.png'),
            name: '物资储备',
            type: 'D'
          },
          {
            src: require('../../assets/zydw.png'),
            name: '专业队伍',
            type: 'E'

          }

        ],
        //表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key: 'rowIndex',
            width: 60,
            align: 'center',
            selectedUser: [],
            customRender: function (t, r, index) {
              return parseInt(index) + 1
            }
          },
          {
            title: '名称',
            align: 'center',
            dataIndex: 'name'
          },
          {
            title: '地址',
            align: 'center',
            dataIndex: 'address'
          },
          {
            title: '防护等级',
            align: 'center',
            dataIndex: 'grade'
          }
        ],

        height: {
          height: '',
        },
        url: {
          list: '/emergencySource/emergencySource/list',
          remark: '/emergencySource/emergencySource/remark',
          remarkList: '/emergencySource/emergencySource/remarkList',
          queryById: '/emergencySource/emergencySource/queryById',
          callPhone: window._CONFIG['callPhoneURL'] + '/PhoneCall=',
          locate:'/emergencySource/emergencySource/getDataSource'

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
      init(x, y) {
        let that = this
        that.map = new AMap.Map('container', {
          center: [x, y],
          resizeEnable: true,
          zoom: 10
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
              that.init(result.position.lng, result.position.lat);
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
          address: record.address
        }
        getAction(that.url.locate, data).then(res => {
          if (res.status) {
            let s = res.geocodes[0].location.split(",");
            let lng = s[0];
            let lat = s[1];
            this.init(lng, lat)
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
          var icon = that.selectIcon(type, id)
          let param = {
            relateId: that.relateId,
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
            icon: icon,
            offset: new AMap.Pixel(-13, -30)
          })
          that.map.add(marke)
          that.marker.push(marke)
        })

      },
      //获取当前标记图标
      onChange(e) {
        let that = this
        that.map.remove(that.marker)          //精度
        that.marker = [];
        //根据类型渲染标记
        let param = {
          type: that.targetValue
        }
        getAction(that.url.remarkList, param).then(res => {
          for (let i = 0; i < res.length; i++) {
            let m = that.renderingMarker(res[i].id, res[i].type, res[i].longitude, res[i].latitude);
            that.marker.push(m)
          }
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
          that.isCircleSelectInfo = true
          that.emergencySource.A = [];
          that.emergencySource.B = [];
          that.emergencySource.C = [];
          that.emergencySource.D = [];
          that.emergencySource.F = [];
          overlays.push(e.obj)
          circle = new AMap.Circle({
            center: e.obj.getCenter(),
            radius: e.obj.getRadius(), //半径,
            fillColor: '#00b0ff',
            strokeColor: '#80d8ff'
          })
          that.map.add(circle)
          // circle.setMap(hat.map)
          let param = {
            type: ''
          }
          getAction(that.url.remarkList).then(res => {
            for (var i = 0; i < res.length; i++) {
              var p = [res[i].longitude, res[i].latitude]
              if (circle.contains(p)) {
                let m = that.renderingMarker(res[i].id, res[i].type, res[i].longitude, res[i].latitude);
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
      renderingMarker(id, type, lng, lat) {
        let that = this;
        var icon = that.selectIcon(type, id)
        // 将 Icon 实例添加到 marker 上:
        var marker = new AMap.Marker({
          position: new AMap.LngLat(lng, lat),
          icon: icon,
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
                "<tr><td>名称：</td><td>" + record.name + "</td></tr>" +
                "<tr><td>地址：</td><td>" + record.address + "</td></tr>" +
                "<tr><td>联系人：</td><td>" + record.contactsName + "</td></tr>" +
                "<tr><td>联系人电话：</td>  <td><a id='" + id + "' >" + record.contactsPhone + "</a></td></tr>" +
                "<tr><td>级别：</td><td>" + record.grade + "</td></tr>" +
                "</table>", //设置文本标注内容
                direction: 'top',//设置文本标注方位
              });
              var btns = document.getElementById(id);
              btns.onclick = function () {
                let url = that.url.callPhone + record.contactsPhone + '?'
                postAction(url).then(res => {
                })
              };
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
                that.init(record.longitude, record.latitude)
                let m = that.renderingMarker(record.id, record.type, record.longitude, record.latitude)
                that.marker.push(m)
                that.testevent(record.id, record.type)
              }
              that.relateId = record.relatedId;
              that.type = record.type;
            }
          }
        }
      },

      selectIcon(type, id) {
        let that = this;
        let icon = []
        if (type == 'A') {
          icon = new AMap.Icon({
            image: require('../../assets/wxy.png'),
            size: new AMap.Size(36, 40), //图标大小
            imageSize: new AMap.Size(36, 40),
            imageOffset: new AMap.Pixel(0, -3)
          })
          that.emergencySource.A.push(id);
        } else if (type == 'B') {
          icon = new AMap.Icon({
            image: require('../../assets/fhmb.png'),
            size: new AMap.Size(40, 40), //图标大小
            imageSize: new AMap.Size(40, 40),
            imageOffset: new AMap.Pixel(-3, -3)
          })
          that.emergencySource.B.push(id);
        } else if (type == 'C') {
          icon = new AMap.Icon({
            image: require('../../assets/zydw.png'),
            size: new AMap.Size(40, 40), //图标大小
            imageSize: new AMap.Size(40, 40),
            imageOffset: new AMap.Pixel(0, 0)
          })
          that.emergencySource.C.push(id);
        } else if (type == 'D' || type == 'E') {
          icon = new AMap.Icon({
            image: require('../../assets/yjjg.png'),
            size: new AMap.Size(37, 37), //图标大小
            imageSize: new AMap.Size(37, 37),
            imageOffset: new AMap.Pixel(0, -3)
          })
          that.emergencySource.D.push(id);
        } else if (type == 'F') {
          icon = new AMap.Icon({
            image: require('../../assets/cbk.png'),
            size: new AMap.Size(37, 37), //图标大小
            imageSize: new AMap.Size(37, 37),
            imageOffset: new AMap.Pixel(0, -3)
          })
          that.emergencySource.F.push(id);
        }
        return icon;
      },
      callPhone() {
        console.log("333333333")
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