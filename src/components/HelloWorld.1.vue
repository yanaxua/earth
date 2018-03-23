<template>
  <div id="earthContent">
    <div key="earth" id="earth" :class="['list-item',bdMap?'earthHidden':'']">
    </div>
    <div key="world" v-show="worldShow" id="world" :class="'list-item'"></div>
    <!--故障列表滑动框-->
    <div id="legend" v-show="legendShow">
      <!--隐藏图例图片-->
      <div v-show="false" v-for="(item,key) in legendPath" :key="key">
        <img :src="item.path" alt="key" draggable="false">
        <span v-text="item.name" :class="item.type"></span>
      </div>
    </div>
    <!--企业列表-->
    <div id="companyLis">
      <a class="btnBor" @click="circle998()">
        <img src="static/image/servieBtn.png">服务目录管理
      </a>
      <div v-for="(item,index) in equipmentList" :key="index" @click="toErr(item)">
        <img :src="'static/logo/'+item.path+'.png'" :alt="item.industryName">
        <span v-text="item.industryName"></span>
      </div>
    </div>
    <div class="legend_industry" v-show="legendShow">
      <ul @mouseover="hoverFlag = false" @mouseout="hoverFlag = true">
        <li v-for="(item,index) in errEquipment" :key="item.id" @click="toErr(item)" :title="item.industryName">
          <span class="name" v-text="item.name"></span>
          <span class="warning" v-text="item.describe"></span>
          <span class="time" v-text="item.time"></span>
        </li>
      </ul>
    </div>
    <div class="legend_input" v-show="legendShow">
      <el-select v-model="industryName" filterable placeholder="搜索企业" @change="industryChange">
        <el-option v-for="(item,index) in equipmentList" :key="item.id" :label="item.industryName" :value="item.id">
        </el-option>
      </el-select>
    </div>
    <div id="sunburst" ref="sunburst" :class="listShow?'showLis':'hiddenLis'">
      <button @click="listShow = false">X</button>
      <div class="circleOne">
        <ul class="circle">
          <li class="inncircle circle0 circle01">
            <span :class="['innercircle1',activeOuter==1?'active':'']"></span>
          </li>
          <li class="inncircle circle0 circle02">
            <span :class="['innercircle1',activeOuter==2?'active':'']"></span>
          </li>
          <li class="inncircle circle0 circle03">
            <span :class="['innercircle1',activeOuter==3?'active':'']"></span>
          </li>
          <li class="inncircle circle0 circle04">
            <span :class="['innercircle1',activeOuter==4?'active':'']"></span>
          </li>
          <li class="inncircle circle0 circle05">
            <span :class="['innercircle1',activeOuter==5?'active':'']"></span>
          </li>
          <li class="inncircle circle0 circle06">
            <span :class="['innercircle1',activeOuter==6?'active':'']"></span>
          </li>
          <li class="inncircle circle0 circle07">
            <span :class="['innercircle1',activeOuter==7?'active':'']"></span>
          </li>
          <li class="inncircle circle0 circle08">
            <span :class="['innercircle1',activeOuter==8?'active':'']"></span>
          </li>
          <li class="inncircle circle1"></li>
          <li class="inncircle circle2">
            <span :class="['one',activeInner==1?'active':'']"></span>
            <span :class="['two',activeInner==2?'active':'']"></span>
          </li>
          <li class="inncircle circle3"></li>
          <li class="inncircle circle4"></li>
          <li class="inncircle circle5"></li>
        </ul>
        <ul class="optionLis">
          <!--<li v-for="(item,index) in linkLis" :key="index" :class="['circle00'+(index+1),activeOuter==index+1?'active':'']" @click="'circle00'+(index+1)" v-text="item.name"></li>-->
          <li :class="['circle001',activeOuter==1?'active':'']" @click="circle001">计量与传感</li>
          <li :class="['circle002',activeOuter==2?'active':'']" @click="circle002">计量传感网络</li>
          <li :class="['circle003',activeOuter==3?'active':'']" @click="circle003">IT网络</li>
          <li :class="['circle004',activeOuter==4?'active':'']" @click="circle004">IDC</li>
          <li :class="['circle005',activeOuter==5?'active':'']" @click="circle005">监测报告</li>
          <li :class="['circle006',activeOuter==6?'active':'']" @click="circle006">故障定位</li>
          <li :class="['circle007',activeOuter==7?'active':'']" @click="circle007">故障修复</li>
          <li :class="['circle008',activeOuter==8?'active':'']" @click="circle008">远程托管</li>
          <li :class="['circle009',activeInner==1?'active':'']" @click="circle009">能管</li>
          <li :class="['circle010',activeInner==2?'active':'']" @click="circle010">中央空调</li>
        </ul>
        <div class="innerContent">
          <p class="fl">
            <span class="value" v-text="randomNumber"></span>
            <span class="title">设备总数</span>
          </p>
          <ul class="fr">
            <li>
              <span class="title">故障</span>
              <span class="value" v-text="Math.floor(randomNumber/200)"></span>
            </li>
            <li>
              <span class="title">运维次数</span>
              <span class="value" v-text="Math.floor(randomNumber/100)"></span>
            </li>
            <li>
              <span class="title">无故障运行时间</span>
              <span class="value" v-text="Math.floor(randomNumber*2.5)+'h'"></span>
            </li>
          </ul>
        </div>
      </div>
      <div class="chooseOne">
      </div>
      <div>
        <!--<div id="echarts1"></div>-->
        <div id="echarts2"></div>
      </div>
    </div>
    <div class="aiframe" v-show="iframeShow">
      <button @click="iframeShow = false">X</button>
      <iframe name="aiframe" id="aiframe" src="#" frameborder="1" width="100%" height="100%"></iframe>
    </div>
  </div>
</template>
<script>
import echartsGl from 'echarts-gl'
import BMap from 'BMap'
/**导入世界地图 */
import world from 'echarts/map/js/world'
import china from 'echarts/map/js/china'
export default {
  components: {},
  data() {
    return {
      earth: {
        globe: {
          backgroundColor: '#000',
          environment: 'static/image/starfield.jpg',
          baseTexture: 'static/image/earth.jpg',
          // heightTexture: '../../static/image/elev_bump.jpg',
          // displacementScale: 0.1,
          shading: 'color',
          layers: [{
            type: 'blend',
            texture: ""
          }],
          viewControl: {
            autoRotate: false,
            zoomSensitivity: 3,
            distance: 220,
            alpha: 0,
            beta: 90,
            animation: false,
            /*开启过渡动画*/
            targetCoord: [108.56, 34.15] /*定位坐标,开启忽略alpha,beta*/
          }
        }
      },
      nation: {
        backgroundColor: {
          color: "#fff"
        },
        geo: {
          type: 'map',
          map: 'world',
          top: 0,
          left: 0,
          right: 0,
          bottom: 0,
          boundingCoords: [
            [-180, 90],
            [180, -90]
          ],
          itemStyle: {
            normal: {
              areaColor: 'transparent',
              borderColor: '#fff'
            },
            emphasis: {
              areaColor: 'yellow'
            }
          },
          label: {
            show: false
          }
        },
        series: []
      },
      earthIns: null,
      nationIns: null,
      equipmentList: [
        {
          industryName: '韶关永盛山庄',
          value: [113.580262, 24.808615, 500],
          id: 1,
          path: "deli",
          type: "normal",
          equipment: ["中央空调#450", "中央空调#551", "能管设备#071", "能管设备#071"],
          errEquipment: [{
            name: "中央空调#450",
            describe: "能耗过高",
            time: "02-01/11:02:45",
            type: "warning"
          }]
        },
        {
          industryName: '茂名汇丰酒店',
          value: [110.924231, 21.670108, 180],
          id: 2,
          path: "dianzi",
          type: "warning",
          equipment: ["中央空调#856", "能管设备#789", "能管设备#071"],
          errEquipment: [{
            name: "能管设备#789",
            describe: "数据异常",
            time: "02-01/11:02:45",
            type: "warning"
          }]
        },
        {
          industryName: '汕尾东鹏建材',
          value: [115.410654, 22.816221, 90],
          id: 3,
          path: "dongyu",
          type: "danger",
          equipment: ["中央空调#410", "中央空调#645", "能管设备#566"],
          errEquipment: [{
            name: "能管设备#566",
            describe: "CPU达到60%",
            time: "01-22/16:33:32",
            type: "danger"
          }, {
            name: "中央空调#645",
            describe: "冷却水温度超标",
            time: "01-29/08:02:36",
            type: "warning"
          }]
        },
        {
          industryName: '梅州百胜电脑',
          value: [116.132646, 24.30242, 87],
          id: 4,
          path: "fujiayi",
          type: "danger",
          equipment: ["中央空调#750", "中央空调#895", "能管设备#451"],
          errEquipment: [{
            name: "能管设备#451",
            describe: "服务器数据异常",
            time: "01-29/08:02:36",
            type: "danger"
          }]
        },
        {
          industryName: '广州东宝大厦',
          value: [113.317982, 23.137914, 87],
          id: 5,
          path: "fuwa",
          type: "normal",
          equipment: ["中央空调#D235", "中央空调#D695", "能管设备#D551"],
          errEquipment: [{
            name: "中央空调#D695",
            describe: "服务器数据异常",
            time: "01-29/08:02:36",
            type: "danger"
          }]
        }
      ],
      errEquipment: [],
      industryName: "",
      /*用于搜索框*/
      timerId: null,
      legendPath: {
        normal: {
          name: "正常",
          path: "static/image/legend_normal.png"
        },
        warning: {
          name: "警告",
          path: "static/image/legend_warning.png"
        },
        danger: {
          name: "故障",
          path: "static/image/legend_danger.png"
        }
      },
      legendShow: true,
      worldShow: false,
      listShow: false,
      /**菜单显隐*/
      iframeShow: false,
      /**弹框显隐*/
      hoverFlag: true,
      /*滚动框标记*/
      bdMap: null,
      /**百度地图实例 */
      markerLis: [],
      /**百度地图当前标记点*/
      activeOuter: 3,
      activeInner: 1,
      echarts1: {
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross',
            label: {
              backgroundColor: '#6a7985'
            }
          }
        },
        legend: {
          data: ['输风机能耗', '压缩机能耗'],
          textStyle: {
            color: "#75afff"
          }
        },
        grid: {
          left: '0',
          right: '0',
          bottom: '0',
          top: "0",
          containLabel: true
        },
        xAxis: [{
          type: 'category',
          boundaryGap: false,
          data: ['周一', '周二', '周三', '周四', '周五', '周六', '周日'],
          axisLabel: {
            color: "#75afff"
          }
        }],
        yAxis: [{
          type: 'value',
          axisLabel: {
            color: "#75afff"
          }
        }],
        series: [{
          name: '输风机能耗',
          type: 'line',
          stack: '总量',
          areaStyle: {
            normal: {}
          },
          data: [120, 132, 101, 134, 90, 230, 210]
        },
        {
          name: '压缩机能耗',
          type: 'line',
          stack: '总量',
          areaStyle: {
            normal: {}
          },
          data: [220, 182, 191, 234, 290, 330, 310]
        }
        ]
      },
      echarts2: {
        title: {
          text: "故障率",
          left: "center",
          top: -2,
          textStyle: {
            color: "#75afff"
          }
        },
        color: ['#3398DB'],
        tooltip: {
          trigger: 'axis',
          axisPointer: { // 坐标轴指示器，坐标轴触发有效
            type: 'shadow' // 默认为直线，可选为：'line' | 'shadow'
          }
        },
        grid: {
          left: '1%',
          right: '1%',
          bottom: '1%',
          top: "16%",
          containLabel: true
        },
        xAxis: [{
          type: 'category',
          data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
          axisTick: {
            alignWithLabel: true
          },
          axisLabel: {
            color: "#75afff"
          }
        }],
        yAxis: [{
          type: 'value',
          axisLabel: {
            color: "#75afff"
          }
        }],
        series: [{
          name: '故障率',
          type: 'bar',
          barWidth: '60%',
          data: [10, 8, 3, 5, 6, 7, 5]
        }]
      },
      randomNumber: 555,
      /**随机数 */
      currentMarker: null,
      /**之前点击的marker,用于判断信息显隐*/
      linkLis: [{
        name: "计量与传感",
        path: "jiliangqiju_nengguan"
      },
      {
        name: "计量传感网络",
        path: "zhinengchuanganwangluo"
      },
      {
        name: "IT网络",
        path: "itwangluo_nengguan"
      },
      {
        name: "IDC",
        path: "idc_nengguan"
      },
      {
        name: "监测报告",
        path: "jiancebaogao_neirong_nengguan"
      },
      {
        name: "故障定位",
        path: "keshihuajiance"
      },
      {
        name: "故障修复",
        path: "guzhangxiufu_xiangqing"
      },
      {
        name: "远程托管",
        path: "yuanchengyemian"
      },
      {
        name: "能管",
        path: ""
      },
      {
        name: "中央空调",
        path: ""
      },
      ]
    }
  },
  computed: {},
  created() {
    /*创建时获取数据*/
    for (let i = 0; i < this.equipmentList.length; i++) {
      var item = this.equipmentList[i];
      for (let j = 0; j < item.errEquipment.length; j++) {
        var temp = item.errEquipment[j];
        temp.value = item.value;
        temp.industryName = item.industryName;
        this.errEquipment.push(temp);
      }
    }
    for (let i = 0; i < this.equipmentList.length; i++) {
      var temp = this.equipmentList[i];
      var tempObj = {
        type: 'scatter',
        coordinateSystem: 'geo',
        geoIndex: 0,
        data: [temp],
        symbol: "image://" + this.legendPath[temp.type].path,
        symbolOffset: [0, '-50%'],
        symbolSize: 27,
        label: {
          normal: {
            formatter: function(params, ticket, callback) {
              return params.data.equipment.length;
            },
            show: true
          },
          emphasis: {
            formatter: function(params, ticket, callback) {
              var arr = [
                '{name|' + params.data.industryName + '}',
                '{hr|}'
              ];
              params.data.equipment.forEach(function(element, index) {
                arr.push('{equipment|' + (index + 1) + '.' + element + '}');
              });
              return arr.join("\n");
            },
            color: "#000",
            rich: {
              name: {
                fontSize: 16,
                color: "#000"
              },
              hr: {
                width: "100%",
                borderColor: "rgba(0,0,0,0.6)",
                borderWidth: 1,
                height: 0,
                lineHeight: 12,
              },
              equipment: {
                fontSize: 16,
                color: "#000",
                lineHeight: 20,
              }
            },
            position: "top",
            backgroundColor: "#eee",
            borderColor: '#555',
            borderWidth: 2,
            borderRadius: 5,
            padding: 10,
            show: true
          }
        }
      }
      this.nation.series[i] = tempObj;
    }
    var canvas = document.createElement('canvas');
    this.nationIns = this.$echarts.init(canvas, null, {
      width: 2048,
      height: 1024
    });
    this.nationIns.setOption(this.nation);
    this.nationIns.on("click", this.showWorld);
  },
  mounted() {
    let me = this;
    // me.toDeep()
    this.earthIns = this.$echarts.init(document.getElementById("earth"));
    this.earthIns.showLoading();
    this.earth.globe.layers[0].texture = this.nationIns;
    this.earthIns.setOption(this.earth);
    // /**转动到指定位置 */
    // /**窗口重置的监听 */
    window.addEventListener("resize", me.echartsResize());
    setTimeout(function() {
      me.earthIns.hideLoading();

      //   // var echarts1 = me.$echarts.init(document.getElementById("echarts1"));
      var echarts2 = me.$echarts.init(document.getElementById("echarts2"));
      //   // echarts1.setOption(me.echarts1);
      echarts2.setOption(me.echarts2)
      setInterval(function() {
        // me.echarts1.series[0].data.shift();
        // me.echarts1.series[0].data.push((180 + Math.random() * 50));
        // echarts1.setOption(me.echarts1);
        me.echarts2.series[0].data.shift();
        me.echarts2.series[0].data.push((1 + Math.random() * 5));
        echarts2.setOption(me.echarts2);
      }, 1000);
      var timerId = setTimeout(function() {
        me.toDeep();
      }, 2000)
      var clearTimerId = function() {
        clearTimeout(timerId);
      }
      window.addEventListener("click", clearTimerId);
      setInterval(function() {
        if (me.hoverFlag) {
          var temp = me.errEquipment.splice(0, 1);
          me.errEquipment.push(...temp);
        }
      }, 2000);
    }, 1000)
  },
  beforeRouteLeave(to, from, next) {
    /*摧毁echarts实例*/
    /**取消resize监听 */
    window.removeEventListener("resize", this.echartsResize());
    this.earthIns.dispose();
    this.nationIns.dispose();
    next();
  },
  methods: {
    toDeep(row) {
      let me = this;
      if (this.bdMap == null) {
        this.earthIns.setOption({
          globe: {
            viewControl: {
              autoRotate: false,
              distance: 70,
              animation: true,
              /*开启过渡动画*/
              animationDurationUpdate: 1500,
              animationEasingUpdate: "linear",
              targetCoord: [113.31, 23.14]
            }
          }
        });
      }
      /**百度地图展示*/
      var timeA = me.bdMap ? 10 : 1500;
      setTimeout(function() {
        me.worldShow = true;
        me.$nextTick(() => {
          var list = document.querySelector("#earthContent .legend_industry ul");
          list.classList.add("hidden");
          if (me.bdMap == null) {
            /**创建百度地图实例 */
            me.bdMap = new BMap.Map("world");
            me.bdMap.addControl(new BMap.MapTypeControl({
              mapTypes: [BMAP_NORMAL_MAP, BMAP_SATELLITE_MAP]
            }));
            me.bdMap.setMapStyle({
              styleJson: [{
                "featureType": "water",
                "elementType": "all",
                "stylers": {
                  "color": "#021019"
                }
              },
              {
                "featureType": "highway",
                "elementType": "geometry.fill",
                "stylers": {
                  "visibility": "off"
                }
              },
              {
                "featureType": "highway",
                "elementType": "geometry.stroke",
                "stylers": {
                  "visibility": "off"
                }
              },
              {
                "featureType": "arterial",
                "elementType": "geometry.fill",
                "stylers": {
                  "color": "#000000"
                }
              },
              {
                "featureType": "arterial",
                "elementType": "geometry.stroke",
                "stylers": {
                  "color": "#0b3d51"
                }
              },
              {
                "featureType": "local",
                "elementType": "geometry",
                "stylers": {
                  "color": "#000000",
                }
              },
              {
                "featureType": "land",
                "elementType": "all",
                "stylers": {
                  "color": "#08304b"
                }
              },
              {
                "featureType": "railway",
                "elementType": "geometry.fill",
                "stylers": {
                  "color": "#000000"
                }
              },
              {
                "featureType": "railway",
                "elementType": "geometry.stroke",
                "stylers": {
                  "color": "#08304b"
                }
              },
              {
                "featureType": "subway",
                "elementType": "geometry",
                "stylers": {
                  "lightness": -70
                }
              },
              {
                "featureType": "building",
                "elementType": "geometry.fill",
                "stylers": {
                  "color": "#000000"
                }
              },
              {
                "featureType": "all",
                "elementType": "labels.text.fill",
                "stylers": {
                  "color": "#857f7f"
                }
              },
              {
                "featureType": "all",
                "elementType": "labels.text.stroke",
                "stylers": {
                  "color": "#000000"
                }
              },
              {
                "featureType": "building",
                "elementType": "geometry",
                "stylers": {
                  "color": "#022338"
                }
              },
              {
                "featureType": "green",
                "elementType": "geometry",
                "stylers": {
                  "color": "#062032"
                }
              },
              {
                "featureType": "boundary",
                "elementType": "all",
                "stylers": {
                  "color": "#1e1c1c"
                }
              },
              {
                "featureType": "boundary",
                "elementType": "geometry",
                "stylers": {
                  "hue": "#2e8bca"
                }
              },
              {
                "featureType": "manmade",
                "elementType": "geometry",
                "stylers": {
                  "color": "#022338"
                }
              },
              {
                "featureType": "poi",
                "elementType": "all",
                "stylers": {
                  "visibility": "off"
                }
              },
              {
                "featureType": "all",
                "elementType": "labels.icon",
                "stylers": {
                  "visibility": "off"
                }
              },
              {
                "featureType": "all",
                "elementType": "labels.text.fill",
                "stylers": {
                  "color": "#2da0c6",
                  "visibility": "on"
                }
              },
              {
                "featureType": "districtlabel",
                "elementType": "labels",
                "stylers": {}
              },
              {
                "featureType": "administrative",
                "elementType": "geometry",
                "stylers": {
                  "hue": "#2e8bca"
                }
              }
              ]
            });
            me.bdMap.enableScrollWheelZoom(true); /**滚轮缩放 */
            me.bdMap.enableDoubleClickZoom(true); /**双击缩放 */
            // me.bdMap.enableInertialDragging(true);/**惯性拖动 */
            me.bdMap.enableContinuousZoom(true); /**连续缩放效果 */
            me.bdMap.centerAndZoom(new BMap.Point(108.56, 34.15), 5);
          }
          var tempFlag = 0; /*用于判断之前是否有过点,用于第一次只进入某一个数据时*/
          me.markerLis.forEach(item => {
            me.bdMap.removeOverlay(item);
            tempFlag++;
          })
          /**计算位置中心,以及添加标注*/
          me.markerLis = [];
          var [minX, maxX, minY, maxY] = [360, 0, 360, 0];
          for (let i = 0; i < me.equipmentList.length; i++) {
            let temp = me.equipmentList[i];
            if (!row) {
              temp.value[0] <= minX && (minX = temp.value[0]);
              temp.value[0] >= maxX && (maxX = temp.value[0]);
              temp.value[1] <= minY && (minY = temp.value[1]);
              temp.value[1] >= maxY && (maxY = temp.value[1]);
            }
            var [centerX, centerY] = [(minX + maxX) / 2, (minY + maxY) / 2];
            if (!row || (row && row.industryName == temp.industryName)) {
              let point = new BMap.Point(temp.value[0], temp.value[1]); /*创建数据点*/
              me.equipmentList[i].point = point; /*添加到数据属性*/
              let myIcon0 = new BMap.Icon("static/image/circle.png", new BMap.Size(120, 120), { /*创建图标*/
                imageSize: new BMap.Size(0, 0),
                anchor: new BMap.Size(60, 60)
              });
              let myIcon1 = new BMap.Icon("static/image/circle.png", new BMap.Size(120, 120), { /*创建图标*/
                imageSize: new BMap.Size(120, 120),
                anchor: new BMap.Size(60, 60)
              });
              let html0 = `<div class="circleContent">
                    <img class="circleRotate circleRotate1" src="static/circle/1.png" alt="">
                    <img class="circleRotate circleRotate3" src="static/circle/3.png" alt="">
                    <img class="circleRotate circleRotate5" src="static/circle/5.png" alt="">
                    <img class="circleRotate circleRotate8" src="static/circle/8.png" alt="">
                    <p class="labelNumber">${(Math.random() + "").substr(3, 2)}</p>
                    <div class="circleInfo">
                    <p class="circleTitle"> <<  ${temp.industryName}</p>`;
              for (let k = 0; k < temp.equipment.length; k++) {
                var tempStr = temp.equipment[k];
                html0 += `<span>${k + 1} 、${tempStr}</span>`;
              }
              html0 += "</div></div>";
              let label0 = new BMap.Label(html0, {
                offset: new BMap.Size(-50, -50)
              }); /*图片大小和文本大小的差值/2*/
              label0.setStyle({
                width: "220px",
                height: "220px",
                cursor: "pointer",
                backgroundColor: "transparent",
                border: "none",
                display: "block"
              });
              let marker = new BMap.Marker(point, {
                icon: myIcon1
              });
              marker.addEventListener("mouseover", function(e) {
                // if (me.currentMarker) {
                marker.setIcon(myIcon0);
                // if (marker.getLabel()) {
                label0.setStyle({
                  display: "block"
                });
                // }
                marker.setLabel(label0);
              });
              marker.addEventListener("mouseout", function(e) {
                marker.setIcon(myIcon1);
                label0.setStyle({
                  display: "none"
                });
              });
              marker.addEventListener("click", function(e) {
                me.bdMap.panTo(e.point);
                !me.listShow && (me.listShow = true);
                me.randomNumber = Math.floor(350 + Math.random() * 150);
              })
              me.markerLis.push(marker);
              me.bdMap.addOverlay(marker);
              if (row && row.industryName == temp.industryName) {
                me.listShow = true;
                me.randomNumber = Math.floor(350 + Math.random() * 150);
                break;
              }
            }
          }
          /**设置地图中心*/
          if (row) {
            /**页面初始化之前需设定中心点,未设置不能进行平移panto操作*/
            tempFlag == 0 ? me.bdMap.centerAndZoom(new BMap.Point(row.value[0], row.value[1]), 9) : me.bdMap.panTo(
              new BMap.Point(row.value[0], row.value[1]));
            // tempFlag == 0 ? me.bdMap.setViewport({ center: new BMap.Point(row.value[0], row.value[1]), zoom: 10 }) : me.bdMap.panTo(new BMap.Point(row.value[0], row.value[1]));
            // me.bdMap.setViewport({ center: new BMap.Point(row.value[0], row.value[1]), zoom: 10 })
            // window.open("./yanshi/jiliangqiju.html");
          } else {
            me.bdMap.centerAndZoom(new BMap.Point(centerX, centerY), 8);
            var markerClusterer = new BMapLib.MarkerClusterer(me.bdMap, {
              markers: me.markerLis
            });
            // me.bdMap.setViewport({ center: new BMap.Point(row.value[0], row.value[1]), zoom: 10 })
          }
          // me.bdMap.setViewport({ center: new BMap.Point(108.56,34.15), zoom: 10 },{enableAnimation:true})
          /**浏览器定位当前地址*/
          // var geolocation = new BMap.Geolocation();
          // geolocation.getCurrentPosition(function(r) {
          //   if (this.getStatus() == BMAP_STATUS_SUCCESS) {
          //     console.log(r);
          //     // var mk = new BMap.Marker(r.point);
          //     // var label = new BMap.Label("您当前的位置 - " + r.address.city, { offset: new BMap.Size(20, -10) });/*创建文字框*/
          //     // label.setStyle({ cursor: "pointer" });
          //     // mk.setLabel(label);
          //     // me.bdMap.addOverlay(mk);
          //   }
          //   else {
          //     alert('failed' + this.getStatus());
          //   }
          // });

          /**IP定位当前地址*/
          // function myFun(result) {
          //   var cityName = result.name;
          //   console.log(result);
          //   var mk = new BMap.Marker(result.center);
          //   var label = new BMap.Label("您当前的位置 - " + result.name, { offset: new BMap.Size(20, -10) });/*创建文字框*/
          //   label.setStyle({ cursor: "pointer" });
          //   mk.setLabel(label);
          //   me.bdMap.addOverlay(mk);
          // }
          // var myCity = new BMap.LocalCity();
          // myCity.get(myFun);
        })
      }, timeA);
    },
    toErr(row) {
      this.toDeep(row);
    },
    showWorld(parm) {
      if (parm.name && parm.name == "China") {
        this.toDeep();
      } else if (this.equipmentList.some(item => item.industryName == parm.data.industryName)) {
        // this.$router.push({ name: 'layout', params: { username: parm.name } });
        // window.location.href = "./yanshi/jiliangqiju.html";
        // window.open("./yanshi/jiliangqiju.html");
        this.listShow = true;
        // this.$router.push({ name: 'layout' });
      }
    },
    echartsResize() {
      return () => {
        this.throttle(this.earthIns.resize(), 100);
      }
    },
    throttle(fn, delay) {
      let me = this;
      me.timerId = null;
      return function() {
        clearTimeout(me.timerId);
        me.timerId = setTimeout(function() {
          fn();
        }, delay);
      }
    },
    industryChange(val) {
      for (let i = 0; i < this.equipmentList.length; i++) {
        var item = this.equipmentList[i];
        if (val == item.id) {
          console.log(val);
          this.toErr(item);
          break;
        }
      }
    },
    newPromise(time, resolve) {
      return new Promise((resolve, reject) => {
        setTimeout(resolve(), time);
      });
    },
    circle001() {
      this.iframeShow = true;
      this.activeOuter = 1;
      window.open('./yanshi/jiliangqiju_nengguan.html', 'aiframe');
    },
    circle002() {
      this.iframeShow = true;
      this.activeOuter = 2;
      window.open('./yanshi/zhinengchuanganwangluo.html', 'aiframe');
    },
    circle003() {
      this.iframeShow = true;
      this.activeOuter = 3;
      window.open('./yanshi/itwangluo_nengguan.html', 'aiframe');
    },
    circle004() {
      this.iframeShow = true;
      this.activeOuter = 4;
      window.open('./yanshi/idc_nengguan.html', 'aiframe');
    },
    circle005() {
      this.iframeShow = true;
      this.activeOuter = 5;
      window.open('./yanshi/jiancebaogao_neirong_nengguan.html', 'aiframe');
    },
    circle006() {
      this.iframeShow = true;
      this.activeOuter = 6;
      window.open('./yanshi/keshihuajiance.html', 'aiframe');
    },
    circle007() {
      this.iframeShow = true;
      this.activeOuter = 7;
      window.open('./yanshi/guzhangxiufu_xiangqing.html', 'aiframe');
    },
    circle008() {
      this.iframeShow = true;
      this.activeOuter = 8;
      window.open('./yanshi/yuanchengyemian.html', 'aiframe');
    },
    // DOTO
    circle998() {
      this.iframeShow = true;
      this.activeOuter = 8;
      window.open('./yanshi/yunweifuwudingyue.html', 'aiframe');
    },
    circle009() {
      this.activeInner = 1;
      // this.iframeShow = true;
    },
    circle010() {
      this.activeInner = 2;
      // this.iframeShow = true;
    },
  },
  watch: {}
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
#earthContent {
  position: relative;
  width: 100%;
  height: 100%;
}

.aiframe {
  position: absolute;
  width: 90%;
  height: 90%;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #fff;
  z-index: 999;
}

.aiframe button {
  position: absolute;
  top: 0;
  right: 0;
  padding: 6px;
  cursor: pointer;
}

#aiframe {
  background-color: #fff;
}

#earthContent li {
  list-style: none;
}

#legend {
  position: absolute;
  right: 20px;
  bottom: 20px;
  user-select: none;
  display: flex;
}

#earthContent .legend_industry {
  position: absolute;
  left: 20px;
  bottom: 55px;
  width: 380px;
  /*height: 150px;*/
  color: #ccc;
}

#earthContent .legend_industry ul {
  padding: 10px;
  background-color: rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  padding-right: 6px;
}

#earthContent .legend_industry li {
  user-select: none;
  padding-left: 10px;
  /*border-left: 4px solid #aaa;*/
  cursor: pointer;
}

#earthContent .legend_industry ul.hidden {
  color: #000;
  border: 1px solid #073067;
  background-color: rgba(93, 152, 230, 0.1);
  border-radius: 10px;
}

#earthContent .legend_industry ul.hidden li {
  color: #13589e;
}

#earthContent .hidden li {
  /*background-color: rgba(0, 0, 0, .1);*/
  /*border-left: 4px solid #ff5e00e8;*/
  display: flex;
}

#earthContent .legend_industry li:hover {
  border-left: 4px solid #ff5e00e8;
  background-color: rgba(0, 0, 0, 0.1);
}

#earthContent .legend_industry span {
  font-size: 14px;
  flex: 1;
  color: #71a9f7;
  line-height: 20px;
}

#earthContent .legend_industry .warning {
  color: #ff7c24;
}

#earthContent .hidden .warning {
  color: #ff7c24;
}

#earthContent .legend_industry .danger {
  color: #d81e06;
}

#earthContent .hidden .danger {
  color: red;
}

#earthContent .el-table__header-wrapper,
#earthContent .el-table__body {
  background-color: #ccc;
}

#earthContent .legend_input {
  position: absolute;
  left: 20px;
  top: 7px;
}

#earthContent .legend_input .el-input__inner {
  background-color: #03173c;
  color: #75afff;
}

#legend img {
  width: 25px;
  margin-bottom: 8px;
  vertical-align: middle;
  /*height: 100px;*/
}

#legend span {
  color: #fff;
  font-size: 16px;
  display: inline-block;
}

#legend .normal {
  color: #cdcdcd;
}

#legend .warning {
  color: #f97207;
}

#legend .danger {
  color: #d81e06;
}

#earthContent .el-dialog__wrapper {
  overflow: hidden;
}

.el-select-dropdown {
  background-color: #0c1935 !important;
}

#earthContent .el-dialog__body {
  padding-top: 0;
  height: 90%;
}

#earthContent .el-dialog {
  height: 70%;
}

#world {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  background: url("../../static/image/net.png");
  background-size: 400px 225px;
  background-color: #030b21 !important;
  /*background-color: rgba(54, 89, 139, .8) !important;*/
}

#earth {
  width: 100%;
  height: 100%;
}

#earth.earthHidden {
  opacity: 0;
}

.list-item {
  display: inline-block;
}

.list-enter-active,
.list-leave-active {
  transition: all 1s;
}

.list-enter,
.list-leave-to {
  opacity: 0;
}

#sunburst {
  width: 380px;
  height: 680px;
  position: absolute;
  border: 1px solid #073067;
  background-color: rgba(93, 152, 230, 0.1);
  border-radius: 10px;
  right: 0px;
  top: 60px;
  transform: translate(100%, 0);
}

#sunburst>button {
  position: absolute;
  top: 0;
  right: 0;
  background-color: #75afff;
  cursor: pointer;
}

#sunburst.showLis {
  animation: showLis 0.6s ease forwards;
}

#sunburst.hiddenLis {
  animation: hiddenLis 0.6s ease forwards;
}

#sunburst .rotateLis {
  animation: rotateLis 0.6s ease forwards;
}

#sunburst .circleOne img {
  width: 300px;
}

#sunburst .optionLis {
  position: absolute;
  width: 0;
  height: 0;
  top: 0;
  right: 0;
  color: #75afff;
  z-index: 999;
  display: none;
}

#sunburst .optionLis li {
  position: absolute;
  width: 100px;
  cursor: pointer;
}

#sunburst .optionLis li.active {
  color: #e07619;
}

#sunburst .optionLis .circle001 {
  top: -2px;
  right: 103px;
}

#sunburst .optionLis .circle002 {
  top: 19px;
  right: 34px;
}

#sunburst .optionLis .circle003 {
  top: 64px;
  right: 5px;
}

#sunburst .optionLis .circle004 {
  top: 136px;
  right: -18px;
}

#sunburst .optionLis .circle005 {
  top: 211px;
  right: 0;
}

#sunburst .optionLis .circle006 {
  top: 259px;
  right: 38px;
}

#sunburst .optionLis .circle007 {
  top: 290px;
  right: 106px;
}

#sunburst .optionLis .circle008 {
  top: 272px;
  right: 258px;
}

#sunburst .optionLis .circle009 {
  top: 114px;
  right: 64px;
}

#sunburst .optionLis .circle010 {
  top: 227px;
  right: 150px;
}

#sunburst .innerContent {
  width: 200px;
  height: 200px;
  border-radius: 50%;
  position: absolute;
  top: 50px;
  left: 50px;
  font-size: 12px;
}

#sunburst .innerContent .value {
  color: #e07619;
}

#sunburst .innerContent .title {
  color: #75afff;
}

#sunburst .innerContent p {
  width: 30%;
  position: absolute;
  top: 69px;
  left: 15px;
}

#sunburst .innerContent ul {
  position: absolute;
  top: 61px;
  right: 4px;
}

#sunburst .innerContent li {
  line-height: 21px;
}

#sunburst .innerContent p .value {
  font-size: 24px;
  display: block;
}

#sunburst .innerContent p .title {}

#sunburst .circle {
  width: 300px;
  height: 300px;
  position: relative;
}

#sunburst .inncircle {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: transparent;
  border-radius: 50%;
}

#sunburst .inncircle span {
  position: absolute;
  display: inline-block;
  top: 50%;
  left: 50%;
  transform-origin: top left;
  background-color: #75afff;
}

#sunburst .inncircle span.active {
  background-color: #e07619;
}

#sunburst .circle0 {
  width: 94%;
  height: 94%;
  overflow: hidden;
  transform-origin: center center;
}

#sunburst .circle0 span {
  width: 180px;
  height: 180px;
  top: 50%;
  left: 50%;
  transform-origin: top left;
}

#sunburst .circle01 {
  transform: translate(-50%, -50%) rotateZ(-130deg);
}

#sunburst .circle02 {
  transform: translate(-50%, -50%) rotateZ(-100deg);
}

#sunburst .circle03 {
  transform: translate(-50%, -50%) rotateZ(-70deg);
}

#sunburst .circle04 {
  transform: translate(-50%, -50%) rotateZ(-40deg);
}

#sunburst .circle05 {
  transform: translate(-50%, -50%) rotateZ(-10deg);
}

#sunburst .circle06 {
  transform: translate(-50%, -50%) rotateZ(20deg);
}

#sunburst .circle07 {
  transform: translate(-50%, -50%) rotateZ(50deg);
}

#sunburst .circle08 {
  transform: translate(-50%, -50%) rotateZ(80deg);
}

#sunburst .circle0 .innercircle1 {
  width: 160px;
  height: 160px;
  transform: skew(42deg, 26deg) rotateZ(0deg);
}

#sunburst .circle0 .innercircle1 {
  width: 160px;
  height: 160px;
  transform: skew(42deg, 26deg) rotateZ(0deg);
}

#sunburst .circle1 {
  width: 260px;
  height: 260px;
  border: 2px dashed #75afff;
  background-color: #0c1935;
}

#sunburst .circle2 {
  width: 240px;
  height: 240px;
  border: 8px solid #36598b;
  overflow: hidden;
  background-color: #0c1935;
}

#sunburst .circle2 .one {
  width: 160px;
  height: 160px;
  transform: skewY(-19deg) rotateZ(-59deg);
}

#sunburst .circle2 .two {
  width: 160px;
  height: 160px;
  transform: skewY(30deg) rotateZ(13deg);
}

#sunburst .circle3 {
  width: 200px;
  height: 200px;
  border: 1px solid #75afff;
  background-color: #0c1935;
}

#sunburst .chooseOne {
  height: 30px;
  margin-left: 40px;
  margin-top: 10px;
}

#sunburst .chooseOne select {
  border-radius: 5px;
  color: #75afff;
  font-size: 14px;
  line-height: 18px;
  border: 1px solid #75afff;
  background-color: #0c1935;
}

#sunburst .chooseOne select:last-child {
  margin-left: 20px;
}

#companyLis {
  position: absolute;
  width: 180px;
  height: 340px;
  border-radius: 10px;
  top: 70px;
  left: 20px;
  border: 1px solid #073067;
  background-color: rgba(93, 152, 230, 0.1);
  padding: 10px;
}

#companyLis>div {
  display: flex;
  margin-bottom: 15px;
  cursor: pointer;
}

#companyLis img {
  height: 50px;
}

#companyLis span {
  width: 80px;
  text-align: center;
  line-height: 50px;
  color: #75afff;
  width: 160px;
}

#echarts1 {
  width: 90%;
  height: 160px;
}

#echarts2 {
  width: 95%;
  height: 320px;
}

.imgRotate {
  animation: imgRotate 4s linear infinite;
}

.labelDiv {
  position: absolute;
  top: -110px;
  left: -110px;
  width: 650px;
  height: 340px;
}

.circleContent {
  width: 220px;
  height: 220px;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  animation: toWidth 0.8s ease forwards;
}

.labelNumber {
  position: absolute;
  width: 100px;
  height: 100px;
  text-align: center;
  line-height: 100px;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: #fff;
  font-size: 56px;
  font-weight: 700;
}

.circleInfo {
  position: absolute;
  top: 50%;
  left: 72%;
  width: 300px;
  height: 200px;
  transform: translate(0, -50%);
  background-image: url("../../static/image/kuang.png");
  background-repeat: no-repeat;
  background-size: contain;
  padding-left: 30px;
  opacity: 0;
  animation: infoShow 0.6s 0.2s ease forwards;
}

.circleInfo .circleTitle {
  font-size: 18px;
  color: #d06e1a;
  margin-top: 28px;
}

.circleInfo span {
  display: block;
  font-size: 14px;
  color: #fff;
}

.circleRotate {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 100%;
  animation-name: circleRotate;
  animation-timing-function: linear;
  animation-iteration-count: infinite;
  user-select: none;
}

.circleRotate1 {
  animation-duration: 16s;
  animation-direction: normal;
}

.circleRotate2 {
  animation-duration: 20s;
  animation-direction: normal;
  width: 120%;
}

.circleRotate3 {
  animation-duration: 18s;
  animation-direction: normal;
  width: 72%;
}

.circleRotate4 {
  animation-duration: 18s;
  animation-direction: reverse;
  width: 102%;
}

.circleRotate5 {
  animation-duration: 18s;
  animation-direction: normal;
  width: 91%;
}

.circleRotate6 {
  animation-duration: 18s;
  animation-direction: reverse;
  width: 120%;
}

.circleRotate7 {
  animation-duration: 18s;
  animation-direction: normal;
  width: 120%;
}

.circleRotate8 {
  animation-duration: 8s;
  animation-direction: reverse;
  width: 63%;
}

.circleRotate9 {
  animation-duration: 18s;
  animation-direction: normal;
  width: 120%;
}

#companyLis {
  margin-top: 40px;
}

.btnBor {
  line-height: 18px;
  position: absolute;
  top: -50px;
  left: 0px;
  /* bottom: -50px;
    left: 0; */
  padding: 10px 24px;
  color: #fff;
  background-color: rgba(93, 152, 230, 0.1);
  border: 1px solid #073067;
  letter-spacing: 2px;
  color: #75afff;
  cursor: pointer;
  /* box-shadow: 0 0 6px 0px #4F82BE; */
}

#companyLis .btnBor img {
  width: 18px;
  height: 18px;
  margin-right: 12px;
  display: inline-block;
  vertical-align: top;
}

.btnBor:hover {
  box-shadow: 0 0 10px 0px #4f82be;
}







/* .btnBor:before {
    content: "";
    position: absolute;
    width: 10px;
    height: 10px;
    border-width: 3px 0 0 3px;
    border-style: solid;
    border-color: #4F82BE;
    top: -6px;
    left: -6px;
  }

  .btnBor:after {
    content: "";
    position: absolute;
    width: 10px;
    height: 10px;
    border-width: 0 3px 3px 0;
    border-style: solid;
    border-color: #4F82BE;
    bottom: -6px;
    right: -6px;
  } */

@keyframes circleRotate {
  from {
    transform: translate(-50%, -50%) rotateZ(0deg);
  }
  to {
    transform: translate(-50%, -50%) rotateZ(360deg);
  }
}

@keyframes rotateLis {
  from {
    transform: rotateY(0deg);
  }
  to {
    transform: rotateY(360deg);
  }
}

@keyframes showLis {
  from {
    transform: translate(100%, 0);
  }
  to {
    transform: translate(0%, 0);
  }
}

@keyframes hiddenLis {
  from {
    transform: translate(0%, 0);
  }
  to {
    transform: translate(100%, 0);
  }
}

@keyframes imgRotate {
  from {
    transform: rotateZ(0deg);
  }
  to {
    transform: rotateZ(360deg);
  }
}

@keyframes toWidth {
  from {
    transform: translate(-50%, -50%) scale(0.5, 0.5);
  }
  to {
    transform: translate(-50%, -50%) scale(1, 1);
  }
}

@keyframes infoShow {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style>
