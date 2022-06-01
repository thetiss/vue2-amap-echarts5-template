<template>
  <div class="card">
    <div class="title">{{ info && info.parkName }}</div>
    <div class="content">
      <div class="graph">
        <div ref="pie" :style="{ width: '100%', height: '100%' }"></div>
      </div>
      <div class="list">
        <p class="item" v-for="(item, index) in infoList" :key="index">
          <span class="name">{{ item.name }}：</span>
          <span class="val">{{ item.value }}</span>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "AMap",
  props: {
    info: {
      type: Object,
      default: null,
    },
    // dataList: {
    //   type: Array,
    //   default: function () {
    //     return [];
    //   },
    // },
  },
  data() {
    return {
      // 环图
      myEcharts: null,
    };
  },
  computed: {
    infoList: function () {
      let list = [];
      if (this.info.dataList.length > 0) {
        list = this.info.dataList.map((item) => {
          return {
            name: this.getObjLabel(item.name),
            value: item.value,
          };
        });
      }
      // console.log(31, list);
      return list;
    },
  },
  created() {},
  mounted() {
    this.drawPie();
  },
  methods: {
    drawPie() {
      this.myEcharts = this.$echarts.init(this.$refs.pie);
      let dataVal = 70;
      let Green = {
        x: 0,
        y: 0,
        x2: 0,
        y2: 1,
        colorStops: [
          {
            offset: 0,
            color: "#99da69", // 0% 处的颜色
          },
          {
            offset: dataVal / 100,
            color: "#01babc", // 100% 处的颜色
          },
        ],
        globalCoord: false, // 缺省为 false
      };

      let option = {
        title: {
          text: "使用率",
          x: "center",
          y: "85%",
          textStyle: {
            color: "#fff",
            fontSize: 12,
          },
        },
        series: [
          {
            //渐变圆环
            name: "",
            type: "pie",
            radius: [25, 36],
            startAngle: 180,
            hoverAnimation: false,
            avoidLabelOverlap: true,
            z: 0,
            zlevel: 0,
            label: {
              show: false,
              normal: { show: false },
            },
            data: [
              {
                value: dataVal,
                name: "",
                itemStyle: {
                  normal: {
                    color: Green,
                  },
                },
                label: {
                  normal: {
                    formatter: function (params) {
                      return "{title|" + params.value + "}" + "{cell|%}";
                    },
                    position: "center",
                    show: true,
                    textStyle: {
                      rich: {
                        title: {
                          fontSize: 16,
                          fontWeight: "bold",
                          fontFamily: "Adobe Heiti Std",
                          color: ["#ffffff"],
                        },
                        cell: {
                          fontSize: 12,
                          fontWeight: "bold",
                          fontFamily: "Adobe Heiti Std",
                          color: ["#ffffff"],
                          padding: [10, 0, 0, 5],
                        },
                      },
                    },
                  },
                },
              },
            ],
          },
          {
            //仪表盘样式
            name: "",
            type: "gauge",
            // center: ["50%", "42%"],
            radius: "55",
            startAngle: 90,
            clockwise: true,
            splitNumber: 5,
            hoverAnimation: true,
            axisTick: {
              show: false,
            },
            splitLine: {
              length: 10,
              lineStyle: {
                width: 1,
                color: "#fff",
              },
            },
            axisLabel: {
              show: false,
            },
            pointer: {
              show: false,
            },
            axisLine: {
              lineStyle: {
                opacity: 0,
              },
            },
            detail: {
              show: false,
            },
            data: [
              {
                value: Math.round(dataVal),
                name: "",
              },
            ],
          },

          // {
          //   name: "usage",
          //   type: "pie",
          //   clockWise: true,
          //   radius: [30, 40],
          //   itemStyle: {
          //     normal: {
          //       color: "#16A2A7",
          //       shadowColor: "#16A2A7",
          //       shadowBlur: 0,
          //       label: {
          //         show: false,
          //       },
          //       labelLine: {
          //         show: false,
          //       },
          //     },
          //   },
          //   hoverAnimation: false,
          //   // center: [0 * 25 + 10 + "%", "50%"],
          //   data: [
          //     {
          //       value: 23,
          //       label: {
          //         normal: {
          //           formatter: function (params) {
          //             return params.value + "%";
          //           },
          //           position: "center",
          //           show: true,
          //           textStyle: {
          //             fontSize: "16",
          //             fontWeight: "bolder",
          //             color: "#16A2A7",
          //           },
          //         },
          //       },
          //     },
          //     {
          //       value: 100 - 23,
          //       name: "invisible",
          //       itemStyle: {
          //         normal: {
          //           color: "#052F73",
          //         },
          //         emphasis: {
          //           color: "#052F73",
          //         },
          //       },
          //     },
          //   ],
          // },
        ],
      };

      this.myEcharts.setOption(option);
    },
    getObjLabel(key) {
      const nameArrObj = {
        a: "总车位",
        b: "在停车位",
        c: "空闲车位",
        d: "占用车位",
        e: "累计收入(元)",
        f: "总车位",
        g: "巡管员：",
      };
      return nameArrObj[key];
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.card {
  background-color: transparent;
}
.title {
  display: flex;
  align-items: center;
  width: 291px;
  height: 36px;
  font-size: 16px;
  color: #fff;
  position: relative;
  background: transparent;
  margin-left: 5px;
  padding-left: 15px;
}
.title::before {
  content: "";
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  z-index: -1;
  background-image: linear-gradient(
    90deg,
    rgba(3, 100, 255, 0.8) 1%,
    rgba(3, 100, 255, 0.8) 1%,
    rgba(3, 100, 255, 0) 100%,
    rgba(3, 100, 255, 0) 100%
  );
  transform: skew(-15deg);
}
.content {
  display: flex;
  width: 300px;
  height: 105px;
  border-radius: 2px;
  background-color: rgba(0, 32, 80, 0.7);
}
.graph {
  /* width: 40%; */
  flex: 2;
  display: flex;
  justify-content: center;
  align-items: center;
}
.list {
  flex: 3;
  padding: 8px;
}
.item {
  display: inline-flex;
  margin: 5px 4px;
  font-size: 12px;
  color: #fff;
}
.val {
  font-size: 14px;
  font-weight: bolder;
  color: #be6d39;
}
</style>
