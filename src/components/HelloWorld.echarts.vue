<template>
  <div id="earthContent">
    <div key="earth" id="earth" :class="['list-item',worldIns?'earthHidden':'']">
    </div>
    <div key="world" v-show="worldShow" id="world" :class="'list-item'"></div>
    <!--故障列表滑动框-->
    <div id="company" v-text="industryName"></div>
    <div id="legend" v-show="legendShow">
      <!--<div v-for="(item,key) in legendPath" :key="key">
                     <img :src="item.path" alt="key" draggable="false">
                     <span v-text="item.name" :class="item.type"></span>
                    </div>-->
    </div>
    <div id="dataList">
      <!--<img src="../../static/image/001.png" style="width:100%;height:100%" alt="">-->
      <div v-for="(item,index) in dataList" :key="index">
        <img :src="'static/logo/'+item.path+'.png'" :alt="item.name">
        <span v-text="item.name"></span>
      </div>

    </div>
    <div class="legend_industry" v-show="legendShow">
      <ul @mouseover="hoverFlag = false" @mouseout="hoverFlag = true">
        <li v-for="(item,index) in errEquipment" :key="item.id" @click="toErr(item)" :title="item.industryName">
          <span class="name" v-text="item.name"></span>
          <!--<span :class="['describe',item.type]" v-text="item.describe"></span>-->
          <span class="warning" v-text="item.describe"></span>
          <span class="time" v-text="item.time"></span>
        </li>
      </ul>
    </div>
    <div class="legend_input" v-show="legendShow">
      <el-select v-model="industryName" filterable placeholder="搜索企业" @change="industryChange">
        <el-option v-for="(item,index) in equipmentList" :key="item.id" :label="item.industryName" :value="item.industryName">
        </el-option>
      </el-select>
    </div>
    <div id="sunburst" class="sunburst" :class="listShow?'showLis':'hiddenLis'">
      <div class="cicrelOne">
        <ul class="cicrel">
          <li class="innCicrel cicrel0 cicrel01">
            <span class="innerCicrel1"></span>
          </li>
          <li class="innCicrel cicrel0 cicrel02">
            <span class="innerCicrel1"></span>
          </li>
          <li class="innCicrel cicrel0 cicrel03">
            <span class="innerCicrel1"></span>
          </li>
          <li class="innCicrel cicrel0 cicrel04">
            <span class="innerCicrel1"></span>
          </li>
          <li class="innCicrel cicrel0 cicrel05">
            <span class="innerCicrel1"></span>
          </li>
          <li class="innCicrel cicrel0 cicrel06">
            <span class="innerCicrel1"></span>
          </li>
          <li class="innCicrel cicrel0 cicrel07">
            <span class="innerCicrel1"></span>
          </li>
          <li class="innCicrel cicrel0 cicrel08">
            <span class="innerCicrel1"></span>
          </li>
          <li class="innCicrel cicrel1"></li>
          <li class="innCicrel cicrel2">
            <span class="one"></span>
            <span class="two"></span>
          </li>
          <li class="innCicrel cicrel3"></li>
          <li class="innCicrel cicrel4"></li>
          <li class="innCicrel cicrel5"></li>
        </ul>
        <ul class="optionLis" v-show="listShow">
          <li class="cicrel001" @click="cicrel001">计量与传感</li>
          <li class="cicrel002" @click="cicrel002">计量传感网络</li>
          <li class="cicrel003" @click="cicrel003">IT网络</li>
          <li class="cicrel004" @click="cicrel004">IDC</li>
          <li class="cicrel005" @click="cicrel005">监测报告</li>
          <li class="cicrel006" @click="cicrel006">故障定位</li>
          <li class="cicrel007" @click="cicrel007">故障修复</li>
          <li class="cicrel008" @click="cicrel008">远程托管</li>
          <li class="cicrel009" @click="cicrel009">能管</li>
          <li class="cicrel010" @click="cicrel010">中央空调</li>
        </ul>
        <!--<img :src="'../../../static/image/'+imgsrc+'.png'" alt="">-->
        <div class="innerContent">
          <p class="fl">
            <span class="value">1835</span>
            <span class="title">设备总数</span>
          </p>
          <ul class="fr">
            <li>
              <span class="title">故障</span>
              <span class="value">3</span>
            </li>
            <li>
              <span class="title">运维次数</span>
              <span class="value">7</span>
            </li>
            <li>
              <span class="title">平均运维时间</span>
              <span class="value">2.8h</span>
            </li>
            <!--<li>
                                    <span class="title">当前接入总数</span>
                                    <span class="value">16</span>
                                  </li>
                                  <li>
                                    <span class="title">总流量</span>
                                    <span class="value">127G</span>
                                  </li>
                                  <li>
                                    <span class="title">历史流量峰值</span>
                                    <span class="value">20G</span>
                                  </li>-->
          </ul>
        </div>
      </div>
      <div class="chooseOne">
        <select>
          <option value="volvo">拓扑软件</option>
          <option value="saab">图形软件</option>
        </select>
        <select>
          <option value="volvo">软件园二期</option>
          <option value="saab">软件园一期</option>
          <option value="opel">软件园三期</option>
          <option value="audi">软件园四期</option>
        </select>
        <select>
          <option value="volvo">全部</option>
          <option value="saab">一期</option>
          <option value="opel">二期</option>
          <option value="audi">三期</option>
        </select>
        <select>
          <option value="volvo">月</option>
          <option value="saab">年</option>
          <option value="opel">日</option>
          <option value="audi">周</option>
        </select>
      </div>
      <div>
        <img src="../../static/image/002.png" style="width:100%;" alt="">
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
  components: {
  },
  data() {
    return {
      option: {
        // title: {
        //   text: '迪奥远程运维及代管平台',
        //   subtext: '欢迎登录迪奥平台',
        //   sublink: 'http://www.dyiaw.com',
        //   x: 'center',
        //   textStyle: {
        //     color: 'white',
        //     fontSize: "30"
        //   }
        // },
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
      listShow: false,/**菜单显隐*/
      iframeShow: false,/**弹框显隐*/
      hoverFlag: true,/*滚动框标记*/
      loading: null,
      bdMap: null,/**百度地图实例 */
      markerLis: [],/**百度地图当前标记点*/
      imgsrc: "1",/*图片路径*/
      dataList: [
        { name: "韶关永盛山庄", path: "deli" },
        { name: "茂名汇丰酒店", path: "dianzi" },
        { name: "汕尾东鹏建材", path: "dongyu" },
        { name: "梅州百胜电脑", path: "fujiayi" },
        { name: "广州东宝大厦", path: "fuwa" }
      ]
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
    // window.addEventListener("click", function() {
    //   document.getElementById("aiframe").style.display = "none";
    // })
    window.addEventListener("resize", me.echartsResize());
    setTimeout(function() {
      me.earthIns.hideLoading();
      me.legendShow = true;
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
              animationDurationUpdate: 3000,
              animationEasingUpdate: "linear",
              targetCoord: [113.31, 23.14]
              // targetCoord: [108.56,34.15]
            }
          }
        });
      }

      /**echarts地图展示*/

      document.getElementById("sunburst").classList.remove("rotateLis");
      var timeA = this.worldIns ? 10 : 3000;
      setTimeout(function() {
        me.worldShow = true;
        var option = JSON.parse(JSON.stringify(me.nation));
        me.$nextTick(() => {
          var world = document.getElementById("world");
          if (!me.worldIns) {
            me.worldIns = me.$echarts.init(world);
          }
          // option.backgroundColor = {
          //   color: "#fff"
          // }
          // option.title = {
          //   text: '迪奥科技运维企业地图分布',
          //   x: '10%',
          //   textStyle: {
          //     color: 'white',
          //     fontSize: "30",
          //     textBorderColor: "#000",
          //     textBorderWidth: 2
          //   }
          // }
          option.animation = true;
          option.geo = {
            type: 'map',
            map: 'world',
            roam: true,
            zoom: 16,
            center: [],
            // boundingCoords: [[-180, 90], [180, -90]],
            itemStyle: {
              normal: {
                areaColor: "rgba(3,23,60,.3)",
                borderColor: '#3291d5'
              },
              emphasis: {
                // areaColor: '#67C23A'
                areaColor: "rgba(3,23,60,.3)"
              }
            },
            label: {
              normal: {
                show: false
              }
            }
          };
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
            var [centerX, centerY] = [(minX + maxX) / 2, (minY + maxY) / 2];
            let tempObj = {
              type: 'scatter',
              coordinateSystem: 'geo',
              geoIndex: 0,
              data: [temp],
              symbol: "image://static/image/circle.png",
              // markPoint: {
              //   symbol: "cirtrianglecle",
              //   symbolSize: 16
              // },
              symbolOffset: ['-50%', '-50%'],
              // symbolSize: row ? 0 : 40,
              symbolSize: 120,
              label: {
                normal: {
                  formatter: function(params, ticket, callback) {
                    return params.data.equipment.length;
                  },
                  show: false
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
                      color: "#75afff",
                      backgroundColor: '#030b21'
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
                      color: "#75afff",
                      lineHeight: 20,
                      backgroundColor: '#030b21'
                    }
                  },
                  position: "right",
                  backgroundColor: '#030b21',
                  borderColor: '#555',
                  borderWidth: 2,
                  borderRadius: 5,
                  padding: 10,
                  show: true
                }
              }
            }
            if (row && row.industryName == temp.industryName) {
              tempObj.label.normal = tempObj.label.emphasis;
              tempObj.symbolSize = 120;
              option.series = [tempObj];
              this.listShow = true;
              // document.getElementById("sunburst").classList.add("rotateLis");
              break;
            } else {
              option.series[i] = tempObj;
            }
          }
          option.geo.center = row ? [row.value[0], row.value[1]] : [centerX, centerY];
          var list = document.querySelector("#earthContent .legend_industry ul");
          list.classList.add("hidden");
          me.worldIns.setOption(option, { notMerge: !!row });
          me.worldIns.on("click", me.showChina);
        })
      }, timeA);
    },
    toErr(row) {
      // if (this.worldIns != null) {
      //   window.location.href = "./yanshi/jiliangqiju.html";
      // } else {
      this.toDeep(row);
      // }
    },
    showChina(parm) {
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
    },
    cicrel001() {
      this.iframeShow = true;
      // document.getElementById("aiframe").style.display = "block";
      window.open('./yanshi/jiliangqiju_nengguan.html', 'aiframe');
    },
    cicrel002() {
      this.iframeShow = true;
      window.open('./yanshi/zhinengchuanganwangluo.html', 'aiframe');
    },
    cicrel003() {
      this.iframeShow = true;
      window.open('./yanshi/itwangluo_nengguan.html', 'aiframe');
    },
    cicrel004() {
      this.iframeShow = true;
      window.open('./yanshi/IDC_nengguan.html', 'aiframe');
    },
    cicrel005() {
      this.iframeShow = true;
      window.open('./yanshi/jiancebaogao_neirong_nengguan.html', 'aiframe');
    },
    cicrel006() {
      this.iframeShow = true;
      window.open('./yanshi/keshihuajiance.html', 'aiframe');
    },
    cicrel007() {
      this.iframeShow = true;
      window.open('./yanshi/guzhangxiufu_xiangqing.html', 'aiframe');
    },
    cicrel008() {
      this.iframeShow = true;
      window.open('./yanshi/yuanchengyemian.html', 'aiframe');
    },
    cicrel009() {

      this.iframeShow = true;
    },
    cicrel010() {
      this.iframeShow = true;
    },
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

#company {
  position: absolute;
  top: 20px;
  left: 20px;
  width: 100px;
  height: 20px;
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
  bottom: 2%;
  width: 380px;
  /*height: 150px;*/
  color: #ccc;
}

#earthContent .legend_industry ul {
  padding: 10px;
  background-color: rgba(255, 255, 255, .3);
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
  background-color: rgba(93, 152, 230, .1);
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
  background-color: rgba(0, 0, 0, .1);
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

#earthContent .el-select-dropdown {
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
  background-color: #030b21;
  /*background-color: rgba(3, 11, 33, .8);*/
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
  height: 630px;
  position: absolute;
  border: 1px solid #073067;
  background-color: rgba(93, 152, 230, .1);
  border-radius: 10px;
  right: 0px;
  top: 60px;
}

#sunburst.showLis {
  animation: showLis 0.6s ease forwards;
}

#sunburst.hiddenLis {
  animation: hiddenLis 0.6s ease forwards;
}

#sunburst.rotateLis {
  animation: rotateLis 0.6s ease forwards;
}

#sunburst .cicrelOne img {
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
}

#sunburst .optionLis li {
  position: absolute;
  width: 100px;
  cursor: pointer;
}

#sunburst .optionLis .cicrel001 {
  top: -2px;
  right: 103px;
}

#sunburst .optionLis .cicrel002 {
  top: 19px;
  right: 34px;
}

#sunburst .optionLis .cicrel003 {
  top: 64px;
  right: 5px;
}

#sunburst .optionLis .cicrel004 {
  top: 136px;
  right: -18px;
}

#sunburst .optionLis .cicrel005 {
  top: 211px;
  right: 0;
}

#sunburst .optionLis .cicrel006 {
  top: 259px;
  right: 38px;
}

#sunburst .optionLis .cicrel007 {
  top: 290px;
  right: 106px;
}

#sunburst .optionLis .cicrel008 {
  top: 272px;
  right: 258px;
}

#sunburst .optionLis .cicrel009 {
  top: 114px;
  right: 64px;
}

#sunburst .optionLis .cicrel010 {
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
  margin-top: 40%;
  margin-left: 10px;
}

#sunburst .innerContent ul {
  margin-top: 34%;
  margin-right: 14px;
}

#sunburst .innerContent li {
  line-height: 21px;
}

#sunburst .innerContent p .value {
  font-size: 24px;
  display: block;
}

#sunburst .innerContent p .title {}

#sunburst .cicrel {
  width: 300px;
  height: 300px;
  position: relative;
}

#sunburst .innCicrel {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: transparent;
  border-radius: 50%;
}

#sunburst .innCicrel span {
  position: absolute;
  display: inline-block;
  top: 50%;
  left: 50%;
  transform-origin: top left;
  background-color: #75afff;
}

#sunburst .innCicrel span.active {
  background-color: #e07619;
}

#sunburst .cicrel0 {
  width: 94%;
  height: 94%;
  overflow: hidden;
  transform-origin: center center;
}

#sunburst .cicrel0 span {
  width: 180px;
  height: 180px;
  top: 50%;
  left: 50%;
  transform-origin: top left;
}

#sunburst .cicrel01 {
  transform: translate(-50%, -50%) rotateZ(-130deg);
}

#sunburst .cicrel02 {
  transform: translate(-50%, -50%) rotateZ(-100deg);
}

#sunburst .cicrel03 {
  transform: translate(-50%, -50%) rotateZ(-70deg);
}

#sunburst .cicrel04 {
  transform: translate(-50%, -50%) rotateZ(-40deg);
}

#sunburst .cicrel05 {
  transform: translate(-50%, -50%) rotateZ(-10deg);
}

#sunburst .cicrel06 {
  transform: translate(-50%, -50%) rotateZ(20deg);
}

#sunburst .cicrel07 {
  transform: translate(-50%, -50%) rotateZ(50deg);
}

#sunburst .cicrel08 {
  transform: translate(-50%, -50%) rotateZ(80deg);
}

#sunburst .cicrel0 .innerCicrel1 {
  width: 160px;
  height: 160px;
  transform: skew(42deg, 26deg) rotateZ(0deg);
}

#sunburst .cicrel0 .innerCicrel1 {
  width: 160px;
  height: 160px;
  transform: skew(42deg, 26deg) rotateZ(0deg);
}

#sunburst .cicrel1 {
  width: 260px;
  height: 260px;
  border: 2px dashed #75afff;
  background-color: #0c1935;
}

#sunburst .cicrel2 {
  width: 240px;
  height: 240px;
  border: 8px solid #36598b;
  overflow: hidden;
  background-color: #0c1935;
}

#sunburst .cicrel2 .one {
  width: 160px;
  height: 160px;
  transform: skewY(-19deg) rotateZ(-59deg);
}

#sunburst .cicrel2 .two {
  width: 160px;
  height: 160px;
  transform: skewY(30deg) rotateZ(13deg);
}

#sunburst .cicrel3 {
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

#dataList {
  position: absolute;
  width: 180px;
  height: 390px;
  border-radius: 10px;
  top: 70px;
  left: 20px;
  border: 1px solid #073067;
  background-color: rgba(93, 152, 230, .1);
  padding: 10px;
}

#dataList>div {
  display: flex;
  margin-bottom: 15px;
}

#dataList img {
  height: 50px;
}

#dataList span {
  width: 80px;
  text-align: center;
  line-height: 50px;
  color: #75afff;
  width: 160px;
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
    width: 0;
    display: none;
  }
  to {
    width: 380px;
  }
}

@keyframes hiddenLis {
  from {
    width: 380px;
  }
  to {
    width: 0;
    display: none;
  }
}
</style>