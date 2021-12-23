<template>
  <div id="cesiumContainer"></div>
</template>

<script setup>
import * as Cesium from 'cesium';
import {onMounted} from 'vue';

onMounted(() => {
  // const viewer = new Cesium.Viewer('cesiumContainer');

  // 使用自己指定的图块来提供图像，并且不希望别人去修改它（可以在Viewer中第二个参数配置）
  // 使用arcgis云服务器托管的图像
  var custom = new Cesium.ArcGisMapServerImageryProvider({
    // url: '//services.arcgisonline.com/ArcGIS/rect/services/World_Street_Map/MapServer'
    url: '//deyihu.github.io/src/maptalks-tileLayercollection/examples/?tdsourcetag=s_pcqq_aiomsg'
  });

  // 创建view查看器，指定地图的图块是什么（Viewer构造函数）
  var viewer = new Cesium.Viewer('cesiumContainer', {

    //不创建baseLayerPicker小部件（不想看右上角到图标）
    // baseLayerPicker:false,
    // 设置提供的图像程序 custom
    imagerProvider: custom,
    // 设置cesium世界地形（想要地图是立体的，凹凸的，有地形的）
    terrainProvider: Cesium.createWorldTerrain({
      //请求水面效果
      requestWaterMask: true,
      //请求顶点法线
      requestVertexNormals: true
    })
  });

  //摄像头的配置（即一开始看到的就是中国湖北而不是南非）
  viewer.camera.setView({
    // 设置经纬度和高度
    // destination: Cesium.Cartesian3.fromDegrees(114.3653, 30.2744, 500),
    destination:new Cesium.Cartesian3(1332761,-4662399,4137888),
    // 查看的方向、视角（俯视和仰视）
    orientation: {
      // heading:0.60,
      // 图像旋转90度
      // heading: Cesium.Math.toRadians(0),
      // 度仰角(45抬头看，0平视，90从上往下看）
      // pitch: Cesium.Math.toRadians(-45)

      heading:0.60,
      pitch:-0.66
    }

  });


  // 获取当地的3D建筑
  var city = viewer.scene.primitives.add(new Cesium.Cesium3DTileset({url: Cesium.IonResource.fromAssetId(3839)}));
  // 定义3D样式（通过不同高度设置不同颜色，最后一个透明度）
  city.style = new Cesium.Cesium3DTileStyle({
    color: {
      conditions: [
        ['${Height} >= 300', 'rgba(45,0,75,0.5)'],
        ['${Height} >= 200', 'rgba(102,71,151)'],
        ['${Height} >= 100', 'rgba(170,162,204,0.5)'],
        ['${Height} >= 50', 'color("red")'],
        ['${Height} >= 25', 'rgba(74,92,81,0.5)'],
        ['${Height} >= 10', 'rgba(114,34,82,0.5)'],
        ['${Height} >= 5', 'color("red")'],
        ["true", "rgb(127,59,8)"]
      ]
    },
    show: '${Height} > 0',
    meta: {
      description: '"Building id ${id} has height ${Height}."'
    }
  });


})
</script>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

html, body, #cesiumContainer {
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
  overflow: hidden;
}
</style>
