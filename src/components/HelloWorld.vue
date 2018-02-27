<template>
  <div id="earthContent">
    <transition-group name="fade" mode="out-in">
      <div key="earth" id="earth" class="list-item">
      </div>
      <!--<div key="world" v-show="worldShow" id="world" class="list-item"></div>-->
      <div key="china" v-show="worldShow" id="china"> </div>
    </transition-group>
    <div id="legend" v-show="legendShow">
      <div v-for="(item,key) in legendPath" :key="key">
        <img :src="item.path" alt="key" draggable="false">
        <span v-text="item.name" :class="item.type"></span>
      </div>
    </div>
    <!--故障列表滑动框-->
    <div class="legend_industry" v-show="legendShow">
      <ul @mouseover="hoverFlag = false" @mouseout="hoverFlag = true">
        <li v-for="(item,index) in errEquipment" :key="item.id" @click="toErr(item)" :title="item.industryName">
          <p class="name" v-text="item.name"></p>
          <p :class="['describe',item.type]" v-text="item.describe"></p>
          <p class="time" v-text="item.time"></p>
        </li>
      </ul>
    </div>
    <div class="legend_input" v-show="legendShow">
      <el-select v-model="industryName" filterable placeholder="搜索企业" @change="industryChange">
        <el-option v-for="(item,index) in equipmentList" :key="item.id" :label="item.industryName" :value="item.id">
        </el-option>
      </el-select>
    </div>
    <el-dialog title="提示" :visible.sync="chinaVisible" width="90%" height="100%" style="margin-top: 10px;">
      <!--<div id="china"> </div>-->
    </el-dialog>
  </div>
</template>
<script>
import echartsGl from 'echarts-gl'
import BMap from 'BMap'
/**导入世界地图 */
import world from 'echarts/map/js/world'
import china from 'echarts/map/js/china'
export default {
  components: {
  },
  data() {
    return {
      option: {
        title: {
          text: '迪奥远程运维及代管平台',
          subtext: '欢迎登录迪奥平台',
          sublink: 'http://www.dyiaw.com',
          x: 'center',
          textStyle: {
            color: 'white',
            fontSize: "30"
          }
        },
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
            animation: false,/*开启过渡动画*/
            targetCoord: [108.56, 34.15]/*定位坐标,开启忽略alpha,beta*/
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
          boundingCoords: [[-180, 90], [180, -90]],
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
      china: {
        backgroundColor: {
          color: "#fff"
        },
        geo: {
          type: 'map',
          map: 'china',
          zoom: 1.2,
          itemStyle: {
            normal: {
              // areaColor: "transparent",
              color: "yellow"
            }
          }
        },
        series: [{
          name: '业务分布',
          type: 'scatter',
          coordinateSystem: 'geo',
          data: [],
          symbolSize: function(val) {
            return val[2] / 20;
          },
          label: {
            normal: {
              formatter: '{b}:{c}',
              position: 'right',
              show: false
            },
            emphasis: {
              show: true
            }
          },
          itemStyle: {
            normal: {
              color: 'red'
            }
          }
        }]
      },
      earthIns: null,
      worldIns: null,
      nationIns: null,
      chinaIns: null,
      startLocation: [0, 0],/**相对于起始位置[90,0]的矫正*/
      endLocation: [108.56, 34.15],/**西安*/
      equipmentList: [
        {
          industryName: '韶关永盛山庄',
          value: [113.580262, 24.808615, 500],
          id: 1,
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
      industryName: "",/*用于搜索框*/
      chinaVisible: false,
      timerId: null,
      legendPath: {
        normal: { name: "正常", path: "static/image/legend_normal.png" },
        warning: { name: "警告", path: "static/image/legend_warning.png" },
        danger: { name: "故障", path: "static/image/legend_danger.png" }
      },
      legendShow: false,
      worldShow: false,
      chinaShow: false,
      hoverFlag: true,/*滚动框标记*/
      loading: null,
      bdMap: null,/**百度地图实例 */
      markerLis: []/**百度地图当前标记点*/
    }
  },
  computed: {
    // errEquipment() {
    //   var tempList = [];
    //   for (let i = 0; i < this.equipmentList.length; i++) {
    //     var item = this.equipmentList[i];
    //     for (let j = 0; j < item.errEquipment.length; j++) {
    //       var temp = item.errEquipment[j];
    //       temp.value = item.value;
    //       tempList.push(temp);
    //     }
    //   }
    //   return tempList;
    // }
  },
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
                lineHeight: 12
              },
              equipment: {
                fontSize: 16,
                color: "#000",
                lineHeight: 20
              }
            },
            position: "top",
            backgroundColor: '#eee',
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
      width: 2048, height: 1024
    });
    this.nationIns.setOption(this.nation);
    this.nationIns.on("click", this.showChina);
  },
  mounted() {
    let me = this;
    this.earthIns = this.$echarts.init(document.getElementById("earth"));
    this.earthIns.showLoading();
    this.option.globe.layers[0].texture = this.nationIns;
    this.earthIns.setOption(this.option);
    /**转动到指定位置 */
    /**窗口重置的监听 */
    window.addEventListener("resize", me.echartsResize());
    setTimeout(function() {
      me.earthIns.hideLoading();
      me.legendShow = true;
      var timerId = setTimeout(function() {
        // window.removeEventListener("click",clearTimerId);
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
    next();
  },
  methods: {
    getBase64Image(img) {
      var canvas = document.createElement("canvas");
      canvas.width = img.width;
      canvas.height = img.height;
      var ctx = canvas.getContext("2d");
      ctx.drawImage(img, 0, 0, img.width, img.height);
      var dataURL = canvas.toDataURL("image/png");
      return dataURL;
      // return dataURL.replace("data:image/png;base64,", "");
    },
    toDeep(row) {
      let me = this;
      if (this.worldIns == null) {
        this.earthIns.setOption({
          globe: {
            viewControl: {
              autoRotate: false,
              distance: 70,
              animation: true,/*开启过渡动画*/
              animationDurationUpdate: 1500,
              animationEasingUpdate: "linear",
              targetCoord: [113.31, 23.14]
              // targetCoord: [108.56,34.15]
            }
          }
        });
      }
      // var timeA = this.worldIns ? 10 : 1500;
      var timeA = row ? 10 : 1500;

      /**echarts地图展示*/
      // setTimeout(function() {
      //   me.worldShow = true;
      //   var option = JSON.parse(JSON.stringify(me.nation));
      //   me.$nextTick(() => {
      //     var world = document.getElementById("world");
      //     if (!me.worldIns) {
      //       me.worldIns = me.$echarts.init(world);
      //     }
      //     option.backgroundColor = {
      //       color: "#fff"
      //     }
      //     option.title = {
      //       text: '迪奥科技运维企业地图分布',
      //       x: '10%',
      //       textStyle: {
      //         color: 'white',
      //         fontSize: "30",
      //         textBorderColor: "#000",
      //         textBorderWidth: 2
      //       }
      //     }
      //     option.geo = {
      //       type: 'map',
      //       map: '广东',
      //       // roam: true,
      //       zoom: 1.2,
      //       // boundingCoords: [[-180, 90], [180, -90]],
      //       itemStyle: {
      //         normal: {
      //           areaColor: '#E6A23C',
      //           borderColor: '#fff'
      //         },
      //         emphasis: {
      //           areaColor: '#67C23A'
      //         }
      //       },
      //       label: {
      //         show: false
      //       }
      //     };
      //     for (let i = 0; i < me.equipmentList.length; i++) {
      //       let temp = me.equipmentList[i];
      //       let tempObj = {
      //         type: 'scatter',
      //         coordinateSystem: 'geo',
      //         geoIndex: 0,
      //         data: [temp],
      //         symbol: "image://" + me.legendPath[temp.type].path,
      //         symbolOffset: [0, '-50%'],
      //         // symbolSize: row ? 0 : 40,
      //         symbolSize: 40,
      //         label: {
      //           normal: {
      //             formatter: function(params, ticket, callback) {
      //               return params.data.equipment.length;
      //             },
      //             show: true
      //           },
      //           emphasis: {
      //             formatter: function(params, ticket, callback) {
      //               var arr = [
      //                 '{name|' + params.data.industryName + '}',
      //                 '{hr|}'
      //               ];
      //               params.data.equipment.forEach(function(element, index) {
      //                 arr.push('{equipment|' + (index + 1) + '.' + element + '}');
      //               });
      //               return arr.join("\n");
      //             },
      //             color: "#000",
      //             rich: {
      //               name: {
      //                 fontSize: 16,
      //                 color: "#000"
      //               },
      //               hr: {
      //                 width: "100%",
      //                 borderColor: "rgba(0,0,0,0.6)",
      //                 borderWidth: 1,
      //                 height: 0,
      //                 lineHeight: 12
      //               },
      //               equipment: {
      //                 fontSize: 16,
      //                 color: "#000",
      //                 lineHeight: 20
      //               }
      //             },
      //             position: "right",
      //             backgroundColor: '#eee',
      //             borderColor: '#555',
      //             borderWidth: 2,
      //             borderRadius: 5,
      //             padding: 10,
      //             show: true
      //           }
      //         }
      //       }
      //       if (row && row.industryName == temp.industryName) {
      //         tempObj.label.normal = tempObj.label.emphasis;
      //         tempObj.symbolSize = 40;
      //         option.series = [tempObj];
      //         break;
      //       } else {
      //         option.series[i] = tempObj;
      //       }
      //     }
      //     var list = document.querySelector("#earthContent .legend_industry ul");
      //     list.classList.add("hidden");
      //     me.worldIns.setOption(option, { notMerge: !!row });
      //     me.worldIns.on("click", me.showChina);
      //   })
      // }, timeA);

      /**百度地图展示*/
      setTimeout(function() {
        me.worldShow = true;
        me.$nextTick(() => {
          // var china = document.getElementById("china");
          var list = document.querySelector("#earthContent .legend_industry ul");
          list.classList.add("hidden");

          if (!me.bdMap) {
            /**创建百度地图实例 */
            me.bdMap = new BMap.Map("china");
          }
          // for (let i = 0; i < me.markerLis.length; i++) {
          //   var temp = me.markerLis[i];
          //   me.bdMap.removeOverlay(temp);
          // }
          me.markerLis.forEach(item => {
            me.bdMap.removeOverlay(item);
          })
          // me.bdMap.clearOverlays();
          /**计算位置中心,以及添加标注*/
          var [minX, maxX, minY, maxY] = [360, 0, 360, 0];
          for (let i = 0; i < me.equipmentList.length; i++) {
            let temp = me.equipmentList[i];
            if (!row) {
              temp.value[0] <= minX && (minX = temp.value[0]);
              temp.value[0] >= maxX && (maxX = temp.value[0]);
              temp.value[1] <= minY && (minY = temp.value[1]);
              temp.value[1] >= maxY && (maxY = temp.value[1]);
            }
            if (!row || (row && row.industryName == temp.industryName)) {
              let point = new BMap.Point(temp.value[0], temp.value[1]);/*创建数据点*/
              me.equipmentList[i].point = point;/*添加到数据属性*/
              var myIcon = new BMap.Icon(me.legendPath[temp.type].path, new BMap.Size(35, 35), {/*创建图标*/
                imageSize: new BMap.Size(35, 35),
                anchor: new BMap.Size(17.5, 35)
              });
              var marker = new BMap.Marker(point, { icon: myIcon });
              marker.addEventListener("click", function(e) {/**标记点点击事件 */
                window.location.href = "./yanshi/yunxingjiance.html";
              })
              me.markerLis.push(marker);
              me.bdMap.addOverlay(marker);
              var label = new BMap.Label(temp.industryName, { offset: new BMap.Size(20, -10) });/*创建文字框*/
              label.setStyle({ cursor: "pointer" });
              label.addEventListener("click", function(e) {/**文本框点击事件 */
                window.location.href = "./yanshi/yunxingjiance.html";
              })
              marker.setLabel(label);
              if (row && row.industryName == temp.industryName) {
                break;
              }
            }
          }
          var [centerX, centerY] = [(minX + maxX) / 2, (minY + maxY) / 2];
          /**设置地图中心*/
          if (row) {
            // me.bdMap.centerAndZoom(new BMap.Point(row.value[0], row.value[1]), 8);
            // me.bdMap.setZoom(11);
            me.bdMap.panTo(new BMap.Point(row.value[0], row.value[1]));
          } else {
            me.bdMap.centerAndZoom(new BMap.Point(centerX, centerY), 8);
          }
          // map.addControl(new BMap.MapTypeControl());
          me.bdMap.addControl(new BMap.MapTypeControl({ mapTypes: [BMAP_NORMAL_MAP, BMAP_SATELLITE_MAP] }));
          me.bdMap.enableScrollWheelZoom(true);
          me.bdMap.enableDoubleClickZoom(true);
        })
      }, timeA);
    },
    toErr(row) {
      // if (this.worldIns != null) {
      //   window.location.href = "./yanshi/yunxingjiance.html";
      // } else {
      this.toDeep(row);
      // }
    },
    showChina(parm) {
      if (parm.name && parm.name == "China") {
        this.toDeep();
      } else if (this.equipmentList.some(item => item.industryName == parm.data.industryName)) {
        // this.$router.push({ name: 'layout', params: { username: parm.name } });
        window.location.href = "./yanshi/yunxingjiance.html";
        // this.$router.push({ name: 'layout' });
      }
    },
    echartsResize() {
      return () => {
        this.throttle(this.earthIns.resize(), 100);
        if (this.chinaVisible && this.chinaIns) {
          this.throttle(this.chinaIns.resize(), 100);
        }
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
          this.toErr(item);
          break;
        }
      }
    },
    newPromise(time, resolve) {
      return new Promise((resolve, reject) => {
        setTimeout(resolve(), time);
      });
    }
  },
  watch: {
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
#earthContent {
  position: relative;
  width: 100%;
  height: 100%;
}

#legend {
  position: absolute;
  right: 20px;
  bottom: 20px;
  user-select: none;
}

#earthContent .legend_industry {
  position: absolute;
  left: 0;
  top: 40%;
  max-height: 400px;
  transform: translate(0, -50%);
  color: #ccc;
  font-size: 16px;
}

#earthContent .legend_industry ul {
  padding: 20px 0 20px 10px;
  background-color: rgba(255, 255, 255, .1);
  border-radius: 8px;
}

#earthContent .legend_industry ul.hidden {
  background-color: rgba(255, 255, 255, .6);
  color: #000;
  box-shadow: 0px 5px 18px #888;
}

#earthContent .legend_industry li {
  margin-bottom: 20px;
  user-select: none;
  padding-left: 10px;
  border-left: 4px solid #aaa;
  cursor: pointer;
}

#earthContent .hidden li {
  border-left: 4px solid #fff;
  background-color: rgba(0, 0, 0, .1);
  border-left: 4px solid #ff5e00e8;
}

#earthContent .legend_industry li:hover {
  border-left: 4px solid #ff5e00e8;
  background-color: rgba(0, 0, 0, .2);
}

#earthContent .legend_industry p {
  height: 20px;
  line-height: 20px;
}

#earthContent .legend_industry .warning {
  color: #e0620d;
}

#earthContent .hidden .warning {
  color: orangered;
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
  right: 10px;
  top: 43px;
}

#legend img {
  width: 30px;
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
  /*background: url("static/image/earth.jpg") no-repeat center;*/
  background: url("../../static/image/earth.jpg") no-repeat center;
}

#earth {
  width: 100%;
  height: 100%;
}

#china {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
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
</style>