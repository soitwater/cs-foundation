# WebGIS

## 压缩数据
- [mapbox-gl-js接入GeoJSON大文件的优化策略](http://jingsam.github.io/2017/10/23/large-geojson-with-mapbox-gl-js.html)
- 点聚合

## 使用gsconfig与geoserver自动发布wms服务
### 步骤
- python需要2.7版本
- 需要安装gsconfig
- 安装命令 pip install gsconfig
- geoserver环境 2.13
- jdk 8（否则geoserver拒绝访问）
- 代码
  ```python
  from geoserver.catalog import Catalog
  cat = Catalog("http://localhost:8080/geoserver/rest")
  ```
### 工作区
- `topp = cat.get_workspace("topp")`
# 添加shapefile数据
  ```js
  shapefile_plus_sidecars = {
     'shp': 'C:/Python27/ArcGIS10.2/states.shp',
     'shx': 'C:/Python27/ArcGIS10.2/states.shx',
     'prj': 'C:/Python27/ArcGIS10.2/states.prj',
     'dbf': 'C:/Python27/ArcGIS10.2/states.dbf'
  }
  ```
### 添加到数据存储区
```js
ft = cat.create_featurestore("name", workspace=topp, data=shapefile_plus_sidecars)
```
### 代码2
```py
from geoserver.catalog import Catalog
geourl = "http://localhost/geoserver/rest"  # the url of geoserver
geocat = Catalog(geourl)  # create a Catalog object
store_name = "00N010E"
data_url = "fiel:C:/Users/CSY/Desktop/test/RGB.tiff"
geostore = geocat.create_coveragestore2(store_name)
geostore.url = data_url
geocat.save(geostore)
```
### 注意
- pip2与pip3需要区分开
- pip2的安装是需要cd到python2的目录下的

### 参考链接
- vscode配置python
https://blog.csdn.net/u013205877/article/details/78883405
- https://blog.csdn.net/soindy/article/details/71108356?utm_source=blogxgwz0
- https://blog.csdn.net/qq_38019321/article/details/77369935?utm_source=blogxgwz1
- https://blog.csdn.net/m_hook/article/details/78350717
- https://blog.csdn.net/weixin_40149992/article/details/80722201
- https://www.v2ex.com/t/371539
- https://blog.csdn.net/u014177758/article/details/73250769?utm_source=blogxgwz0
- https://blog.csdn.net/winner_looser/article/details/81137109
- https://blog.csdn.net/dyrlovewc/article/details/53142798
- https://pypi.org/project/gsconfig-py3/1.0.7/
