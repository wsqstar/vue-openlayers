# OpenLayer Vue
## 入门
### 单一数据源

#### 实现

*使用最新的vue-cli3.x 或 4.x*

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

4. 用vscode打开项目，打开终端，安装openlayers
   openlayers官网：http://openlayers.org

	 `cnpm i -S ol `

**删除**掉HelloWorld.vue
新建 **olmap.vue**组件 （vscode编辑器下可以安装**vetur**和 **vue vscode snippets**两插件方便代码提示及写vue代码-vbase命令）

**components/olmap.vue**

```javascript
<template>
    <div id="map" ref="rootmap">

    </div>
</template>

<script>
//一连串的引入
import "ol/ol.css";
import { Map, View } from "ol";
import TileLayer from "ol/layer/Tile";
import OSM from "ol/source/OSM";
//暴露默认地图
export default {
  data() {
    return {
      map: null
    };
  },
  //疑问
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
        projection: "EPSG:4326",    //使用这个坐标系
        center: [114.064839,22.548857],  //深圳
        zoom: 12
      })
    });
  }
};
</script>

//设置文件格式
<style>
#map{height:100%;}
/*隐藏ol的一些自带元素*/
.ol-attribution,.ol-zoom { display: none;}

</style>
```
**App.vue**

```javascript
<template>
  <div id="app">
    <olmap />
  </div>
</template>

<script>
//引入
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
5. 运行 npm run serve
   ![运行结果](https://upload-images.jianshu.io/upload_images/4342827-93828bbb2531d3e5.png)

#### 解析

##### 实现原理与步骤

1. 首先实现环境：
   1. vue/cli
      1. 全局安装（一台机器**仅需一次**）
      2. 自选代码目录下（常用工作区下），新建vue项目并命名
         1. 选择模板 
            - 默认：babel，eslint
            - 推荐：**vue-router，vuex，sass，babel，eslint**
   2. openlayers
      1. 似乎是**每个项目来一个**
      2. 使用cnpm安装
   
2. 其次删除不需要的文件（组件、文件中链接），新建compoents，配置app.vue
   1. 删除默认的Helloworld.vue
   2. 新建olmap.vue，用来显示地图
   3. 配置App.vue
   
3. 运行 程序

   `npm run serve`

### 多数据源

#### 实现

6、将一些信息放置到配置文件中,src目录下新建config文件夹，建mapconfig.js

**src/config/mapconfig.js**

```javascript
import TileLayer from "ol/layer/Tile"
import TileArcGISRest from 'ol/source/TileArcGISRest'
import OSM from "ol/source/OSM"
import XYZ from 'ol/source/XYZ'

let maptype=2          //0表示部署的离线瓦片地图，1表示OSM,2表示使用Arcgis在线午夜蓝地图服务

var streetmap=function(){
    var maplayer=null;
    switch(maptype){
        case 0:
            maplayer=new TileLayer({
                source: new XYZ({
                    //url:'http://127.0.0.1:7080/streetmap/shenzhen/{z}/{x}/{y}.jpg'
                    //学习时使用了天地图的服务，而不是离线xyz，主要是没准备材料
 url:'http://{a-c}.tile.openstreetmap.org/{z}/{x}/{y}.png'

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
    x:114.064839,     //中心点经度和纬度
    y:22.548857,
    zoom:15,          //地图缩放级别
    streetmap:streetmap
};

//暴露出去
export default mapconfig
```



**src/components/olmap.vue**作相应的更改

```javascript
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
        projection: "EPSG:4326",    //使用这个坐标系
        center: [mapconfig.x,mapconfig.y],  //深圳
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

#### 解析

##### 实现原理与步骤

1. 使用mapconfig.js 实现配置，并暴露出去
2. 将olmap.vue中，所有的数值转为mapconfig定义的变量

##### 区分

1. 与ol3相比，使用变量更加方便
2. 整体逻辑视图没有变化，ol方面仍然是layer图层、source数据源，view视图




## 参考
- [vue+openlayers学习 漫漫江雪](https://www.jianshu.com/p/fd399ad4b7d8)


