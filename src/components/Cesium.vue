<template>
  
  <div id="cesiumContainer">
    <!-- 工具栏区域 -->
    <div class="toolbox">
      <div class="tool1" @click="showprovince">切换行政单位划分</div>
      <div class="tool2" @click="fullscreen">全图</div>
      <div class="tool3" @click="measureDistance">测距</div>
      <div class="tool4" @click="clearMeasurements">清除测距</div>
      <div class="tool5" @click="toggleEagleMap">鹰眼</div>
    </div>
    <!-- 鹰眼图显示区域，其显示受 eagleMapVisible 控制 -->
    <div id="eagle-map" v-show="eagleMapVisible"></div>
    <!-- 使用 Board 组件显示查询结果 -->
    <Board
      :show="showBoard"
      :results="queryResults"
      @close="showBoard = false"
    />
  </div>
  <!-- 功能条组件 -->
  <Functionbar
        class="Functionbar"
        :current-user="currentUser"
        @user-logged-in="handleUserLoggedIn"
  />
</template>

<script>
import Board from "./board.vue";
import Functionbar from "./Functionbar.vue";
let viewer
export default {
  components: {
    Board,
    Functionbar,
  },
  name: "cesium",
  data() {
    return {
      // 省市显示
      visboard:true,
      // 控制鹰眼图的显示
      eagleMapVisible: false,
      // 主 Cesium viewer 实例
      // 鹰眼图的 Cesium viewer 实例
      eagleMapView: null,
      // ---------------多条件查询表格
      // 控制 Board 组件的显示
      showBoard: false,
      // 用于保存查询结果的数据
      queryResults: [],
      highlighted:{
        feature: undefined,
        originalColor: new Cesium.Color(),
      }
    };
  },
  methods: {
    handleUserLoggedIn(username) {
      this.$emit("APPVUE-user-logged-in", username);
    },

    showprovince(){
      if (this.visboard) {
        this.setprovince(viewer);
      } else {
        this.setdistinct(viewer);
      }
      this.visboard = !this.visboard;
    },



    setsingledistinct(viewer,path){
      debugger
      viewer.dataSources.removeAll();
      var primitives = viewer.scene.primitives;
      // 遍历原始图元集合中的每个图元
      primitives.forEach(function (primitive) {
          // 检查图元是否为 Cesium.LabelCollection 类型
          if (primitive instanceof Cesium.LabelCollection) {
              // 获取 LabelCollection 中的所有 label 实例数组
              var labels = primitive.getLabels();
              // 遍历 LabelCollection 中的每个 label 实例并逐个删除
              labels.forEach(function (label) {
                  primitive.remove(label);
              });
          }
      });
      // 刷新视图
      viewer.scene.requestRender();
      this.loaddata(path);

    },


    setdistinct(viewer) {
          viewer.dataSources.removeAll();
          var primitives = viewer.scene.primitives._primitives;
          // 遍历原始图元集合中的每个图元c
          console.log(primitives);
          primitives.forEach(function (primitive) {
          // 检查图元是否为 Cesium.LabelCollection 类型

          if (primitive instanceof Cesium.LabelCollection) {
              primitive.destroy();
          }
          });
        // 刷新视图
        var primitives = viewer.scene.primitives._primitives;
        viewer.scene.requestRender();
        this.loaddata("Cesium/backpage/fuzhoushi.json");
        this.loaddata("Cesium/backpage/longyanshi.json");
        this.loaddata("Cesium/backpage/nanpingshi.json");
        this.loaddata("Cesium/backpage/ningdeshi.json");
        this.loaddata("Cesium/backpage/putianshi.json");
        this.loaddata("Cesium/backpage/quanzhoushi.json");
        this.loaddata("Cesium/backpage/sanmingshi.json");
        this.loaddata("Cesium/backpage/zhangshoushi.json");
        this.loaddata("Cesium/backpage/xiamenshi.json");
    },
    setprovince(viewer){
      // debugger
        viewer.dataSources.removeAll();
        var primitives = viewer.scene.primitives._primitives;
          // 遍历原始图元集合中的每个图元c
          console.log(primitives);
          primitives.forEach(function (primitive) {
          // 检查图元是否为 Cesium.LabelCollection 类型
          if (primitive instanceof Cesium.LabelCollection) {
              primitive.destroy();
          }
          });
        viewer.scene.requestRender();
        this.loaddata("Cesium/backpage/fujian.json");
    },




    loaddata(datapath){
      var dataSource = new Cesium.GeoJsonDataSource();
      const labels = viewer.scene.primitives.add(new Cesium.LabelCollection());
      // 加载GeoJSON数据
      dataSource.load(datapath).then(function () {
        const addedNames = new Set();
        // debugger
        // 添加标签
        dataSource.entities.values.forEach(entity => {
          // console.log(entity._properties._Layer);
            entity.polygon.material = Cesium.Color.WHITE.withAlpha(0.1);
            if (!addedNames.has(entity._name)) {
              var position
              // 如果实体有几何信息
              if (entity.polygon) {
                // debugger
                  // 获取多边形的顶点集合
                  const polygonVertices = entity.polygon.hierarchy.getValue(Cesium.JulianDate.now()).positions;
                  // 计算边界矩形
                  const boundingRectangle = Cesium.Rectangle.fromCartesianArray(polygonVertices);
                  // 计算矩形的中心点地理坐标（经纬度以弧度表示）
                  // 获取中心点的经度和纬度（转换为度）
                  const centerLongitude = entity._properties._centroid._value[0]
                  const centerLatitude = entity._properties._centroid._value[1]
                  // 使用中心点的经纬度作为标签位置
                  position = Cesium.Cartesian3.fromDegrees(centerLongitude, centerLatitude);
              } else {
                  // 如果没有几何信息，则使用默认位置（例如，整个地球的中心点）
                  position = Cesium.Cartesian3.fromDegrees(0, 0);
              }
              labels.add({
                  position: position,
                  text: entity._name,
                  fillColor: Cesium.Color.BLACK,
                  font: '11px',
              });
              addedNames.add(entity._name);
              
            }
        });

      });
      // 创建一个标签集合
      viewer.dataSources.add(dataSource);
      viewer.scene.requestRender();
      // viewer.zoomTo(dataSource);
    },
    // 切换鹰眼图的显示
    toggleEagleMap() {
      this.eagleMapVisible = !this.eagleMapVisible;
      const mainCamera = viewer.camera;
      const eagleCamera = this.eagleMapView.camera;
      eagleCamera.setView({
        destination: mainCamera.position,
        orientation: {
          heading: mainCamera.heading,
          pitch: mainCamera.pitch,
          roll: mainCamera.roll,
        },
      });
    },
    // 使 Cesium 视图进入全屏模式
    fullscreen() {
      if (viewer) {
        viewer.scene.fullscreenElement =
          viewer.scene.fullscreenElement || viewer.scene.canvas;
        if (viewer.scene.fullscreenElement.requestFullscreen) {
          viewer.scene.fullscreenElement.requestFullscreen();
        } else if (viewer.scene.fullscreenElement.mozRequestFullScreen) {
          viewer.scene.fullscreenElement.mozRequestFullScreen();
        } else if (
          viewer.scene.fullscreenElement.webkitRequestFullscreen
        ) {
          viewer.scene.fullscreenElement.webkitRequestFullscreen();
        } else if (viewer.scene.fullscreenElement.msRequestFullscreen) {
          viewer.scene.fullscreenElement.msRequestFullscreen();
        }
      }
    },
    //测距
    measureDistance() {
      if (!this.measure) {
        this.measure = new Cesium.Measure(viewer);
      }
      this.measure.drawLineMeasureGraphics({
        clampToGround: true,
        callback: () => {}
      });
    },
    //清除测距
    clearMeasurements() {
      if (this.measure) {
        this.measure._drawLayer.entities.removeAll();
        this.measure.destroy();
        this.measure = null;
      }
      console.log(this.measure)
    },
    // ---------------多条件查询表格
    // 关闭 Board 组件
    closeBoard() {
      this.showBoard = false;
    },
    // 获取查询结果并显示到 Board 组件
    // 在这里添加获取查询结果的方法
    async getQueryResults() {
      // 模拟从后端获取数据
      this.queryResults = [
        // 插入后端返回的数据
      ];
      this.showBoard = true;
    },
  },
  // 组件加载完毕后的生命周期钩子，用于初始化 Cesium 视图和相关功能
  mounted() {

    Cesium.Ion.defaultAccessToken =
      "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI0NDllMzAxMC00NjM5LTQ2NzYtOTY0ZS1hNTg0NzVkZWRkZTMiLCJpZCI6MTM1MjczLCJpYXQiOjE2ODIzMjIyNTR9.XpFoPlN4hfEmRc4n6cRhaL_jcSP0EXBC7ckUql1PQgA";
    viewer = new Cesium.Viewer("cesiumContainer", {
      selectionIndicator: false, //是否显示指示器组件
      //显示地名
      infoBox : false, //是否显示点击要素之后显示的信息
      //禁用搜索按钮
      // geocoder: false,
      //禁用view home按钮
      homeButton: false,
      // 禁用3D/2D按钮
      sceneModePicker: false,
      //禁用baseLayerPicker图层选择器
      baseLayerPicker: false,
      //禁用NavigationHelpButton使用提示
      navigationHelpButton: false,
      //禁用控制视窗动画的播放速度
      animation: false,
      //禁用展示当前时间和允许用户在进度条上拖动到任何一个指定的时间。
      timeline: false,
      //禁用视察全屏按钮。
      fullscreenButton: false,
    });


        // 创建GeoJSON数据源
    this.setdistinct(viewer);
    // 缩放到加载的数据
    //禁用展示商标版权和数据源
    viewer._cesiumWidget._creditContainer.style.display = "none";
    //相机视角目标地
    const targetPos = Cesium.Cartesian3.fromDegrees(
      117.94281073710378, // 经度
      25.428682747067906, // 纬度
      500000 // 高度
    );
    const heading = Cesium.Math.toRadians(0); // 朝向北方
    const pitch = Cesium.Math.toRadians(-90); // 俯视角度为90度
    const roll = Cesium.Math.toRadians(0); // 不旋转

    // 保存初始视图状态
    const initialViewState = {
      destination: targetPos,
      orientation: {
        heading: heading,
        pitch: pitch,
        roll: roll,
      },
    };

    viewer.camera.setView(initialViewState);
      
    //点击某个点获取该点的位置坐标;
    viewer.canvas.addEventListener("click", (event) => {

      const pickedFeature = viewer.scene.pick(event);

      if(pickedFeature && pickedFeature.hasOwnProperty("id") && pickedFeature.id != undefined && pickedFeature.id.hasOwnProperty('_name')){
                //如果有_polygon的属性的话，就将其颜色改为黄色
        var name = pickedFeature.id._name
        console.log(name);
      }else{
        return;
      }

      const pickedLocation = viewer.camera.pickEllipsoid(
        new Cesium.Cartesian2(event.clientX, event.clientY),
        viewer.scene.globe.ellipsoid
      );

      if (Cesium.defined(pickedLocation)) {
        const cartographic = Cesium.Cartographic.fromCartesian(pickedLocation);
        const longitude = Cesium.Math.toDegrees(cartographic.longitude);
        const latitude = Cesium.Math.toDegrees(cartographic.latitude);
        
        console.log(`${longitude},${latitude}`);
      }
    });


    // Information about the currently highlighted feature
    // Color a feature yellow on hover.
    viewer.screenSpaceEventHandler.setInputAction(
        (movement) => {
          try {

              // 如果之前有高亮的要素，取消高亮
              if (Cesium.defined(this.highlighted.feature) &&Cesium.defined(this.highlighted.feature.polygon)) {
                this.highlighted.feature.polygon.material = this.highlighted.originalColor;
                this.highlighted.feature = undefined;
              }
              // 选择新的要素
              const pickedFeature = viewer.scene.pick(movement.endPosition);
              if (!Cesium.defined(pickedFeature)) {
                return;
              }
              // debugger

              // 高亮要素
              
              //判断是否是label
              // debugger
              if(pickedFeature.primitive instanceof Cesium.Label){
                return;
              }else{
                //如果有_polygon的属性的话，就将其颜色改为黄色
                var name = pickedFeature.id._name
                var allDataSources = viewer.dataSources._dataSources;
                // 遍历所有数据源
                allDataSources.forEach(
                  (dataSource) =>{
                  // 获取数据源中的所有实体数组
                  var allEntities = dataSource.entities.values;

                  // 遍历所有实体
                  for (var i = 0; i < allEntities.length; i++) {
                    // debugger
                      var entity = allEntities[i];
                      if (entity._name === name && entity.polygon!=undefined) {
                          this.highlighted.feature = entity
                          this.highlighted.originalColor = entity.polygon.material;
                          entity.polygon.material = Cesium.Color.YELLOW.withAlpha(0.5);
                          break;
                      }
                      // 处理每个实体，例如获取其属性或进行其他操作

                  }
                  });          
              }
          } catch (error) {
            console.log(error);
            
          }
      },
      Cesium.ScreenSpaceEventType.MOUSE_MOVE
      
    );



    //----------- 罗盘仪和放大缩小功能按钮
    const options = {};
    // 用于在使用重置导航重置地图视图时设置默认视图控制。接受的值是Cesium.Cartographic 和 Cesium.Rectangle.
    // options.defaultResetView = Rectangle.fromDegrees(80, 22, 130, 50)
    options.defaultResetView = new Cesium.Cartographic(
      Cesium.Math.toRadians(111.50623801848565),
      Cesium.Math.toRadians(2.8997206760441205),
      8213979.400955964
    );
    //相机方向
    options.orientation = {
      heading: Cesium.Math.toRadians(350.94452087411315),
      pitch: Cesium.Math.toRadians(-66.6402342251215),
      roll: Cesium.Math.toRadians(360),
    };
    //相机延时
    options.duration = 4; //默认为3s

    // 用于启用或禁用罗盘。true是启用罗盘，false是禁用罗盘。默认值为true。如果将选项设置为false，则罗盘将不会添加到地图中。
    options.enableCompass = true;
    // 用于启用或禁用缩放控件。true是启用，false是禁用。默认值为true。如果将选项设置为false，则缩放控件将不会添加到地图中。
    options.enableZoomControls = true;
    // 用于启用或禁用距离图例。true是启用，false是禁用。默认值为true。如果将选项设置为false，距离图例将不会添加到地图中。
    options.enableDistanceLegend = true;
    // 用于启用或禁用指南针外环。true是启用，false是禁用。默认值为true。如果将选项设置为false，则该环将可见但无效。
    options.enableCompassOuterRing = true;

    //修改重置视图的tooltipe
    options.resetTooltip = "重置视图";
    //修改放大按钮的tooltip
    options.zoomInTooltip = "放大";
    //修改缩小按钮的tooltip
    options.zoomOutTooltip = "缩小";

    //如需自定义罗盘控件，请看下面的自定义罗盘控件
    new CesiumNavigation(viewer, options);
    // 创建鹰眼图 Viewer 实例
    this.eagleMapView = new Cesium.Viewer("eagle-map", {
      //禁用搜索按钮
      geocoder: false,
      //禁用view home按钮
      homeButton: false,
      // 禁用3D/2D按钮
      sceneModePicker: false,
      //禁用baseLayerPicker图层选择器
      baseLayerPicker: false,
      //禁用NavigationHelpButton使用提示
      navigationHelpButton: false,
      //禁用控制视窗动画的播放速度
      animation: false,
      //禁用展示当前时间和允许用户在进度条上拖动到任何一个指定的时间。
      timeline: false,
      //禁用视察全屏按钮。
      fullscreenButton: false,
    });

    //禁用展示商标版权和数据源s
    this.eagleMapView._cesiumWidget._creditContainer.style.display = "none";

    viewer.camera.changed.addEventListener(() => {
      if (this.eagleMapVisible) {
        const mainCamera = viewer.camera;
        const eagleCamera = this.eagleMapView.camera;

        // 将鹰眼图的相机设置为主相机的当前视图
        eagleCamera.setView({
          destination: mainCamera.position,
          orientation: {
            heading: mainCamera.heading,
            pitch: mainCamera.pitch,
            roll: mainCamera.roll,
          },
        });
      }
    });
  },
};
</script>

<style scoped>
#cesiumContainer {
  width: 100%;
  height: 100%;
  position: relative;
}

/* 工具列表 */
.toolbox {
  position: absolute;
  display: flex;
  flex-direction: row;
  top: 20px;
  left: 20px;
  z-index: 1;
}

.toolbox div {
  padding: 5px 7px;
  background-color: rgba(234, 230, 230, 0.797);
  border: 2px solid black;
  transition: background-color 0.3s;
}

.toolbox div:active {
  background-color: #3d98bd;
}

/* 鹰眼图 */
/* 将eagle-map放在Cesium视图的右下角 */
#eagle-map {
  position: absolute;
  bottom: 0;
  right: 0;
  z-index: 3;
  height: 200px;
  width: 300px;
}


.Functionbar {
  width: 35%;
  height: 100%;
}
</style>
