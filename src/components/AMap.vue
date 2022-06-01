<template>
  <div id="container"></div>
</template>

<script>
import AMapLoader from "@amap/amap-jsapi-loader";
import MarkerDetail from "./MarkerDetail.vue";
import Vue from "vue";

export default {
  name: "AMap",
  props: {},
  data() {
    return {
      map: null,
      amapInfoWindow: null,
      testPark: [
        { name: "龙华大道停车点", location: [106.558738, 29.562636] },
        { name: "中山四路停车点", location: [106.559662, 29.564239] },
      ],
      testParkB: [
        [106.556428, 29.567632],
        [106.556931, 29.565308],
        [106.558225, 29.556574],
      ],
      testStaff: [
        [106.558295, 29.562369],
        [106.558533, 29.562294],
        [106.559234, 29.562981],
        [106.559018, 29.562899],
        [106.559409, 29.562561],
      ],
    };
  },
  created() {
    this.getGis();
  },
  methods: {
    getGis() {
      AMapLoader.load({
        key: "8aa34376645105244c5cc2fdba48a5f9",
        version: "2.0",
        AMapUI: {
          version: "1.1",
          plugins: ["overlay/SimpleMarker", "overlay/SimpleInfoWindow"],
        },
      })
        .then((AMap) => {
          // map
          this.map = new AMap.Map("container", {
            center: [106.558584, 29.56],
            zoom: 18,
            pitch: 50,
            rotation: -15,
            viewMode: "3D", //开启3D视图,默认为关闭
            mapStyle: "amap://styles/grey",
            zooms: [2, 20],
            // 支持'bg'（地图背景）、'point'（POI点）、'road'（道路）、'building'（建筑物）
            features: ["bg", "point", "building"],
          });

          // console.log(1, "map 初始化 成功===>", this.map);
          this.setMarker();
        })
        .catch((err) => {
          console.log(1, "map 初始化 失败===>", err);
        });
    },
    // 绘制锚点
    setMarker() {
      this.testPark.map((item, index) => {
        const marker = new AMapUI.SimpleMarker({
          iconLabel: index + 1,
          iconStyle: "red",
          //设置基点偏移
          offset: new AMap.Pixel(-19, -60),
          map: this.map,
          showPositionPoint: false,
          position: item.location,
        });
        // 打开信息窗体
        const that = this;
        marker.on("click", function () {
          // 引入 Vue 组件构造器实例化
          const ContextCardContent = Vue.extend(MarkerDetail);
          // 挂载组件
          const contextCardContent = new ContextCardContent({
            //此处针对子组件的props进行合并覆盖
            propsData: {
              info: {
                parkName: "测试北滨路停车场",
                //TODO
                // 1. 将需要的属性转为数组
                // 2. 在组件中，将name值替换为真实值
                dataList: [
                  { name: "a", value: 52 },
                  { name: "b", value: 32 },
                  { name: "c", value: 20 },
                  { name: "d", value: 0 },
                  { name: "e", value: 52 },
                  { name: "f", value: 32 },
                  { name: "g", value: "张三" },
                ],
              },
            },
          }).$mount();
          // 实例化窗口对象
          const infoWindow = new AMap.InfoWindow({
            isCustom: true,
            content: contextCardContent.$el,
            // 基点指向marker的头部位置
            offset: new AMap.Pixel(0, -31),
          });
          infoWindow.open(that.map, marker.getPosition());
        });

        //绑定鼠标右击事件——弹出右键菜单
        marker.on("rightclick", function () {
          // const options = {
          //   content: "<div><p>标记</p><p>车库详情</p></div>",
          // };
          const options = {
            content: "测试项",
            width: 220,
          };
          const contextMenu = new AMap.ContextMenu(options);
          contextMenu.addItem(
            "标记",
            function () {
              that.map.zoomIn();
            },
            0
          );
          contextMenu.addItem(
            "车库详情",
            function () {
              that.map.zoomIn();
            },
            0
          );
          contextMenu.open(that.map, marker.getPosition());
        });
      });
      //
      this.testParkB.map((location, index) => {
        new AMapUI.SimpleMarker({
          iconLabel: index + 1,
          iconStyle: "lightblue",
          //设置基点偏移
          offset: new AMap.Pixel(-19, -60),
          map: this.map,
          showPositionPoint: false,
          position: location,
        });
      });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
#container {
  height: 1080px;
  width: 1920px;
  margin: 0;
}

.info-title {
  display: flex;
  align-items: center;
  margin: 0;
  width: 291px;
  height: 36px;
  background-color: rgba(3, 100, 255, 0.8);
  color: #fff;
  font-size: 16px;
}
.info-content {
  display: flex;
}
info-graph {
  display: flex;
  justify-content: center;
  align-items: center;
}
info-list {
  flex: 2;
}

.amap-ui-smp-ifwn-container {
  width: 500px;
  height: 200px;
}
.amap-ui-smp-ifwn-content-body {
  background: red;
}
.amap-ui-smp-ifwn-info-title {
  display: none;
}
</style>
