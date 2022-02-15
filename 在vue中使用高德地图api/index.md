# 在vue中使用高德地图api


#### 在vue中使用高德地图api

​	1 创建项目

``` powershell
vue init webpack gaode-demo 
#vue2 版本
```

​	2 安装依赖

```powershell
#安装高德官方的loader:amap-jsapi-loader
npm i @amap/amap-jsapi-loader --save
```

​	3 编写HelloWorld

​		创建一个容器

```vue
<template>
  <div id="container"></div>
</template>
```



```javascript
//导入包
import AMapLoader from '@amap/amap-jsapi-loader';


```



```javascript
//在data中声明map对象
data(){
      return{
        map:null,
     } 
},
```



```javascript
//在methods中添加方法
     initMap(){
        AMapLoader.load({
            key:"",             // 申请好的Web端开发者Key，首次调用 load 时必填
            version:"2.0",      // 指定要加载的 JSAPI 的版本，缺省时默认为 1.4.15
            plugins:[''],       // 需要使用的的插件列表，如比例尺'AMap.Scale'等
        }).then((AMap)=>{
            this.map = new AMap.Map("container",{  //设置地图容器id
                viewMode:"3D",    //是否为3D地图模式
                zoom:5,           //初始化地图级别
                center:[105.602725,37.076636], //初始化地图中心点位置
            });
        }).catch(e=>{
            console.log(e);
        })
    },
//
```



```javascript
mounted(){
    //DOM初始化完成进行地图初始化
    this.initMap();
}
```



​	4 run

```powershell
npm run dev
```

![image-20220119122426229](https://gitee.com/santubian/imges01/raw/master/img/202201191224797.png)

