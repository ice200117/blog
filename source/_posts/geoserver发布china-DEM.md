---
title: geoserver发布china DEM
date: 2020-03-14 17:46:58
tags: gis
---

# geoserver服务搭建
利用[docker镜像](https://github.com/kartoza/docker-geoserver)启动，最好使用docker-compose的脚本。


# 下载 [srtm90m分辨率也地形数据](http://srtm.csi.cgiar.org/download)
我下载了覆盖中国区域的6个分区，解压后，将tif放入 geoserver的data目录下，新建一个dem目录。

# geoserver中发布ImageMosaic数据存储，并发布图层
![](/images/ImageMosaic.png)
## 填写名名称，URL在目录浏览中选择dem目录
## 发布图层，启动所在工作区的WMS服务
## 应该可以在Layer preview 中预览图层了
![](/images/preview.png)

# 获取任意的的高程数据
发布完WMS服务后，可以利用[GetFeatureInfo](https://docs.geoserver.org/stable/en/user/services/wms/reference.html#getfeatureinfo)获取任意经纬度的高程。
api的格式为
http://URL地址/geoserver/工作区名称/wms?SERVICE=WMS&VERSION=1.1.1&REQUEST=GetFeatureInfo&QUERY_LAYERS=工作区名称%3A图层名称&LAYERS=工作区名称%3A图层名称&exceptions=application%2Fvnd.ogc.se_inimage&INFO_FORMAT=application%2Fjson&X=50&Y=50&SRS=EPSG%3A404000&STYLES=&WIDTH=101&HEIGHT=101&BBOX=98.40248107910158%2C26.647682189941406%2C107.27943420410158%2C35.524635314941406

# 利用图层组，发布多图层的服务。

## gdaldem  生成 [hillshade 图层](https://tilemill-project.github.io/tilemill/docs/guides/terrain-data/)

## 发布 省、市、县三个shapefile图层
在每个图层的style中设置显示的缩放级别和显示的样式
{% codeblock  lang:xml%}

<?xml version="1.0" encoding="ISO-8859-1"?>
<StyledLayerDescriptor version="1.0.0"
    xsi:schemaLocation="http://www.opengis.net/sld StyledLayerDescriptor.xsd"
    xmlns="http://www.opengis.net/sld"
    xmlns:ogc="http://www.opengis.net/ogc"
    xmlns:xlink="http://www.w3.org/1999/xlink"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <!-- a named layer is the basic building block of an sld document -->

  <NamedLayer>
    <Name>Default Polygon</Name>
    <UserStyle>
        <!-- they have names, titles and abstracts -->

      <Title>A boring default style</Title>
      <Abstract>A sample style that just prints out a grey interior with a black outline</Abstract>
      <!-- FeatureTypeStyles describe how to render different features -->
      <!-- a feature type for polygons -->

      <FeatureTypeStyle>
        <!--FeatureTypeName>Feature</FeatureTypeName-->
        <Rule>
          <Name>Rule 1</Name>
          <Title>GreyFill BlackOutline</Title>
          <Abstract>Grey fill with a black outline 1 pixel in width</Abstract>

           <MinScaleDenominator>100000</MinScaleDenominator>
      	   <MaxScaleDenominator>7000000</MaxScaleDenominator>
          <!-- like a linesymbolizer but with a fill too -->
          <PolygonSymbolizer>
            <Fill>
              <CssParameter name="fill">#AAAAAA</CssParameter>
              <CssParameter name="fill-opacity">0</CssParameter>
            </Fill>
            <Stroke>
              <CssParameter name="stroke">#aaa</CssParameter>
              <CssParameter name="stroke-width">0.5</CssParameter>
            </Stroke>
          </PolygonSymbolizer>

           <TextSymbolizer>
         <Label>
           <ogc:PropertyName>NL_NAME_2</ogc:PropertyName>
         </Label>
          <Font>
           <CssParameter name="font-family">SimSun</CssParameter>
           <CssParameter name="font-size">12</CssParameter>
           <CssParameter name="font-style">normal</CssParameter>
           <CssParameter name="font-weight">bold</CssParameter>
         </Font>
       </TextSymbolizer>


        </Rule>

        </FeatureTypeStyle>
    </UserStyle>
  </NamedLayer>
</StyledLayerDescriptor>
{% endcodeblock %}


## 发布图层组就可以预览了，还可以发布瓦片服务WMTS, 生成瓦片缓存。方便leaflet中调用。

{% iframe http://tile.zc12369.com/geoserver/gwc/demo/china_dem:chinadem?gridSet=EPSG3857&format=image/png 800px 500px %}
