# OpenLayer Vue
## 入门
### 单一数据源

使用最新的vue-cli3.x 或 4.x

1. 安装vue-cli3.0.1: https://cli.vuejs.org/guide/installation.html

	`cnpm install -g @vue/cli` 
	
	`vue -V 查看版本`
2. 新建项目 `vue create vue-openlayers`,并选择default模版

![选择default](https://upload-images.jianshu.io/upload_images/4342827-7b3c4007f8409e30.png)

3. 运行
```
cd vue-openlayers
npm run serve
```

4、用vscode打开项目，打开终端，安装openlayers
openlayers官网：http://openlayers.org

	 cnpm i -S ol 

**删除**掉HelloWorld.vue
新建 olmap.vue组件 （vscode编辑器下可以安装vetur和 vue vscode snippets两插件方便代码提示及写vue代码-vbase命令）

**components/olmap.vue**

```
<template>
<div id="map" ref="rootmap">

</div>
</template>

<script>
import "ol/ol.css";
import { Map, View } from "ol";
import TileLayer from "ol/layer/Tile";
import OSM from "ol/source/OSM";
export default {
data() {
return {
map: null
};
},
mounted() {
var mapcontainer = this.$refs.rootmap;
this.map = new Map({
target: "map",
layers: [
new TileLayer({
source: new OSM()
})
],
view: new View({
projection: "EPSG:4326", //使用这个坐标系
center: [114.064839,22.548857], //深圳
zoom: 12
})
});
}
};
</script>

<style>
#map{height:100%;}
/*隐藏ol的一些自带元素*/
.ol-attribution,.ol-zoom { display: none;}

</style>
```
**App.vue**

```
<template>
<div id="app">
<olmap />
</div>
</template>

<script>
import olmap from './components/olmap.vue'

export default {
name: 'app',
components: {
olmap
}
}
</script>

<style>
*{padding:0; margin:0;}
html,body{
height: 100%;
}
#app {
height: 100%;
}
</style>
```
5、运行 npm run serve
![运行结果](https://upload-images.jianshu.io/upload_images/4342827-93828bbb2531d3e5.png)

### 多数据源

6、将一些信息放置到配置文件中,src目录下新建config文件夹，建mapconfig.js

**src/config/mapconfig.js**

```
import TileLayer from "ol/layer/Tile"
import TileArcGISRest from 'ol/source/TileArcGISRest'
import OSM from "ol/source/OSM"
import XYZ from 'ol/source/XYZ'

let maptype=2 //0表示部署的离线瓦片地图，1表示OSM,2表示使用Arcgis在线午夜蓝地图服务

var streetmap=function(){
var maplayer=null;
switch(maptype){
case 0:
maplayer=new TileLayer({
source: new XYZ({
url:'http://127.0.0.1:7080/streetmap/shenzhen/{z}/{x}/{y}.jpg'
})
})
break;
case 1:
maplayer=new TileLayer({
source: new OSM()
})
break;
case 2:
maplayer=new TileLayer({
source:new TileArcGISRest({
url:'https://map.geoq.cn/ArcGIS/rest/services/ChinaOnlineCommunity/MapServer'
})
})
break;
}
return [maplayer];
}

var mapconfig={
x:114.064839, //中心点经度和纬度
y:22.548857,
zoom:15, //地图缩放级别
streetmap:streetmap
};

export default mapconfig
```
**src/components/olmap.vue**作相应的更改

```
<template>
<div id="map" ref="rootmap">

</div>
</template>

<script>
import "ol/ol.css"
import { Map, View } from "ol"
import mapconfig from '../config/mapconfig'
export default {
data() {
return {
map: null
};
},
mounted() {
var mapcontainer = this.$refs.rootmap;
this.map = new Map({
target: mapcontainer,
layers: mapconfig.streetmap(),
view: new View({
projection: "EPSG:4326", //使用这个坐标系
center: [mapconfig.x,mapconfig.y], //深圳
zoom: mapconfig.zoom
})
});
}
};
</script>

<style>
#map{height:100%;}
/*隐藏ol的一些自带元素*/
.ol-attribution,.ol-zoom { display: none;}

</style>
```






## 参考
- vue+openlayers学习 漫漫江雪


