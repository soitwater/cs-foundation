# esri-loader使用

## 步骤
- `cnpm i -S esri-loader`
- 在`public/index.html`引入`<link rel="stylesheet" href="https://js.arcgis.com/4.7/esri/css/main.css">`
- 在组件内
  ```js
  import React, { Component } from 'react'
  import esriLoader from 'esri-loader'
   
  export default class ArcGISMap extends Component{
    constructor(props){
      super(props)
      this.tileMapUrl = "http://map.geoq.cn/ArcGIS/rest/services/ChinaOnlineStreetPurplishBlue/MapServer"
    }
    componentDidMount(){
      this.initMap()
    }
    initMap(){
      const mapURL = {
        url : "https://js.arcgis.com/4.7/dojo/dojo.js"
      }
      esriLoader.loadModules([
        "esri/Map",
        "esri/Basemap",
        "esri/layers/TileLayer", 
        "esri/views/MapView",
        "dojo/domReady!"
      ], mapURL).then(([Map,Basemap,TileLayer,MapView])=>{
        let layer = new TileLayer({
          url: this.tileMapUrl       
        })
        let baseMap = new Basemap({
          baseLayers: [layer],  
          title: "Custom Basemap",  
          id: "myBasemap" 
        });
        let map = new Map({
          basemap: baseMap
        });
        let view = new MapView({
          center : [120.2, 32.1],
          map: map,
          container : "mapDiv",
          zoom:5
        });
      })
    }
    render(){
      let style = {
        width: '900px',
        height: '600px'
      }
      return(
        <div>
          <div id="mapDiv" style = {style}></div>
        </div>
      )
    }
  }
  ```
  
## 参考
- [参考教程1](https://www.cnblogs.com/onsummer/p/8594716.html)
- [个人项目 esri-react-demo](https://github.com/soitwaterdemos/esri-react-demo)