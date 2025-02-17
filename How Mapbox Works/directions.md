---
title: Directions 方向导航服务
description: Learn how Mapbox’s directions services work and how to add directions services to applications across platforms.
image: /img/narrative/directions.svg
topics:
  - directions 
prependJs:
  - "import Note from '@mapbox/dr-ui/note';"
  - "import BookImage from '@mapbox/dr-ui/book-image';"
  - "import ChevronousText from '@mapbox/mr-ui/chevronous-text';"
  - "import DemoIframe from '@mapbox/dr-ui/demo-iframe';"
contentType: guide
---

Mapbox provides a collection of APIs to add directions-related services to your application. Generate a route with trip durations, estimated distances, and turn-by-turn directions with the [Mapbox Directions API](https://docs.mapbox.com/api/navigation/#directions), retrieve travel times between many points with the [Mapbox Matrix API](https://docs.mapbox.com/api/navigation/#matrix), retrieve duration-optimized trips between points with the [Mapbox Optimization API](https://docs.mapbox.com/api/navigation/#optimization), or align existing fuzzy routes to the routeable network with the [Mapbox Map Matching API](https://docs.mapbox.com/api/navigation/#map-matching). From routing deliveries efficiently to providing navigation steps for wayfinding, Mapbox provides the tools necessary to integrate directions into your application.

Mapbox提供诸多API（应用程序编程接口）以便添加方向导航相关服务到您的应用中。例如，使用[Mapbox Directions API](https://docs.mapbox.com/api/navigation/#directions)来生成一条路径并配有行程时间，估计距离以及逐向道路导航指示相关信息，使用[Mapbox Matrix API](https://docs.mapbox.com/api/navigation/#matrix)来获取不同地点间的旅途时间，使用[Mapbox Optimization API](https://docs.mapbox.com/api/navigation/#optimization)来获得地点间行程时间最短的路径，使用[Mapbox Map Matching API](https://docs.mapbox.com/api/navigation/#map-matching)来将模糊路径校准至可行合理的道路网路上。从交付高效行程路径安排，到提供具体导航步骤指示，Mapbox准备了许多必要的工具以帮助您将方向导航服务添加到应用中。

This guide provides an overview of how the routing network is created, how to add directions services to applications across platforms, how to provide feedback, and relevant documentation to help you get started.

本篇指南将为您介绍：路径网络是如何建立的，如何将方向导航服务添加到跨平台的应用中去，以及如何给方向导航服务提供反馈意见。您可以参考本篇指南最后的相关文档来开始使用Mapbox方向导航服务。

{{
  <DemoIframe src="/help/demos/how-mapbox-works/how-directions-works.html" />
}}

This example uses the [Mapbox GL Directions plugin](https://github.com/mapbox/mapbox-gl-directions) to add driving, cycling, or walking directions to a web application built with Mapbox GL JS. Type in two locations to view the raw JSON response for that query.

本示例使用了[Mapbox GL Directions插件](https://github.com/mapbox/mapbox-gl-directions)将驾驶，骑行和步行的路径导航添加至一个由Mapbox GL JS构建的网页应用中。输入两个地点（如，地点A和地点B），您便可以在上方看到相关查询的原始JSON代码。

The Mapbox Directions API returns a JSON object containing a route with trip durations, estimated distances, and turn-by-turn instructions. When using the Mapbox GL Directions plugin, all this information will be automatically added to the map when a request is complete. By default, the plugin will also display turn-by-turn instructions. This example hides the turn-by-turn instructions and displays the raw JSON response to illustrate what information is included in the directions response object.

Mapbox Directions API返回的JSON对象包含了所选路径以及有关此路径的行程时间，预估距离以及逐向道路导航指示信息。当使用Mapbox GL Directions插件，所有的这些信息将会在查询请求完成时被自动添加到地图。在默认情况下，此插件会返回逐向道路导航指示信息，而本示例选择了隐藏逐项道路导航指示。示例所展示的原始JSON代码可以为您揭示其返回的方向导航对象具体包含了哪些信息。


## How directions work
## 方向导航如何运作

When you provide two or more points to the Mapbox Directions API, it returns a **route** as a GeoJSON line that you can add to your map, text for **turn-by-turn instructions** that you can choose to display in your application, and the **distance and estimated travel times** for the mode of transportation you selected. There are many other services that extend Mapbox directions that allow you to fix messy GPS traces to the network or optimize trips to multiple stops on a single journey.

当您提供两个或多个地点位置给Mapbox Directions API，它会为您返回：一个GeoJSON line对象作为一条**路径**供您添加至地图上，即将在应用中显示的**逐向道路导航指示**文字信息，以及与所选出行方式相配的**距离和预估行程时间**。此外，还有许多其他服务可以用于拓展Mapbox的方向导航功能，例如，将凌乱的GPS追踪路线修正到交通网络上，优化前往多个目的地的单程路线等。

### The routing network
### 路径网络

A directions service that can create routes and optimized trips requires a robust network of paths with distinct attributes like speed, turn restrictions, and travel mode (for example, motorway, foot path, bike lane). Mapbox's directions services use a network of roads and paths, or *ways*, derived from [OpenStreetMap](http://learnosm.org/), a collaborative project to create a free and editable map of the world.

一个可以创建并优化路径的方向导航服务需要一个强大的交通网络。这个交通网络不仅需要有路径位置信息，还需要包含如速度和转弯限制，出行方式（例如，公路，步行道，骑行道）等的路径属性信息。Mapbox的方向导航服务使用了[OpenStreetMap](http://learnosm.org/)提供的公路和小路网络信息，或者说是*道路*网络信息。OpenStreetMap是一个开源合作项目，它可以创建以供免费使用和编辑的世界地图。

{{
  <Note
    title='Ways'
    imageComponent={<BookImage />}
  >
    <p>A <em>way</em> is an OpenStreetMap term used to describe an ordered list of nodes (points) which normally also has at least one tag, or description. For directions, ways can be roads, foot paths, or bicycle lanes.</p>
  </Note>
}}

{{
  <Note
    title='Ways 道路'
    imageComponent={<BookImage />}
  >
    <p><em>Way (道路)</em> 是一个OpenStreetMap术语。它可以被用来描述一组通常至少有一个标签名称或者内容说明的有序节点（点）。在方向导航服务中，道路可以是公路，步行道或者骑行道。</p>
  </Note>
}}


Contributors to OpenStreetMap have built a vast, routeable network that includes properties like speed limits, turn lane restrictions, and accessibility for bikes and pedestrians. These details provide the framework that the [Open Source Routing Machine](http://project-osrm.org/) (OSRM) needs to calculate the most efficient path for your mode of transportation (driving, cycling, walking).

OpenStreetMap项目的贡献者们已经为大家搭建好了一个巨大且可行合理的道路网络，其中还包括了有关速度和转弯限制，自行车及行人可达性的道路属性信息。这些道路属性信息为[Open Source Routing Machine](http://project-osrm.org/) (OSRM)计算所选出行方式（驾车，骑行，步行）的最优路线提供了框架。


### Travel times
### 行程时间

The Mapbox Directions API, Matrix API, and Optimization API all provide estimated trip durations. The time it takes to travel from one point to another is determined by many factors, including:

Mapbox Directions API，Matrix API和Optimization API都可以为您提供预估行程时间。地点位置间的行程时间由很多因素决定，其中包括：

- The profile used (walking, cycling, or driving).
- The speed stored in the [`maxspeed` tag in OpenStreetMap](http://wiki.openstreetmap.org/wiki/Key:maxspeed).
- Traffic derived from real-time telemetry data (when the traffic profile is used).

- 使用profile的类型，例如，出行方式（步行，骑行或驾驶）。
- OpenStreetMap的[`maxspeed`（最大速度）标签](http://wiki.openstreetmap.org/wiki/Key:maxspeed)中储存的速度。
- 来自实时遥测数据的交通流量（当使用交通流量profile进行分析时）。

### Traffic data
### 交通流量/路况数据

Incorporate real-time traffic into route selection and ETA generation using the `mapbox/driving-traffic` profile with the Mapbox [Directions API](https://docs.mapbox.com/api/navigation/#directions), [Map Matching API](https://docs.mapbox.com/api/navigation/#map-matching), or [Matrix API](https://docs.mapbox.com/api/navigation/#matrix), or add a traffic layer to road geometries on a visual map. Read more about our traffic tileset in [Our map data](/help/how-mapbox-works/mapbox-data/#mapbox-traffic).

您可以通过使用[Mapbox Directions API](https://docs.mapbox.com/api/navigation/#directions), [Map Matching API](https://docs.mapbox.com/api/navigation/#map-matching)或者[Matrix API](https://docs.mapbox.com/api/navigation/#matrix)中的`mapbox/driving-traffic` profile来将实时路况信息纳入路径选择分析中并生成相应的ETA（预计到达时间）。您也可以将一个交通路况图层添加到可视地图的道路几何中去。若您想了解更多交通路况图块集的相关内容，请阅读[我们的地图数据](/help/how-mapbox-works/mapbox-data/#mapbox-traffic)这一章节。

The `mapbox/driving-traffic` profile is available globally, but the accuracy of traffic-dependent travel times vary by country.

请注意尽管`mapbox/driving-traffic` profile有来自世界范围内的路况信息，但是各个国家或地区据此估算出的行程时间的准确度不尽相同。

#### Highly accurate travel times
#### 行程预估时间高度准确的国家或地区

Traffic data collected in these countries is comprehensive across geographies and times, resulting in highly accurate travel times.

以下国家或地区所收集的交通路况数据在时空范围内都比较全面，所以这些国家或地区的行程预估时间有较高的准确度。

<div class="grid grid--gut24">
  <div class="col col--4">
     <ul>
       <li>Australia</li>
       <li>Canada</li>
       <li>Czech Republic</li>
       <li>Estonia</li>
       <li>Gibraltar</li>
       <li>Iceland</li>
     </ul>
  </div>
  <div class="col col--4">
     <ul>
       <li>Ireland</li>
       <li>Korea, Republic of</li>
       <li>Latvia</li>
       <li>Liechtenstein</li>
       <li>Netherlands</li>
     </ul>
  </div>
  <div class="col col--4">
     <ul>
       <li>New Zealand</li>
       <li>Puerto Rico</li>
       <li>Slovakia</li>
       <li>United Kingdom</li>
       <li>United States of America</li>
     </ul>
  </div>
</div>

<div class="grid grid--gut24">
  <div class="col col--4">
     <ul>
       <li>澳大利亚</li>
       <li>加拿大</li>
       <li>捷克</li>
       <li>爱沙尼亚</li>
       <li>直布罗陀</li>
       <li>冰岛</li>
     </ul>
  </div>
  <div class="col col--4">
     <ul>
       <li>爱尔兰</li>
       <li>韩国</li>
       <li>拉脱维亚</li>
       <li>列支敦士登</li>
       <li>荷兰</li>
     </ul>
  </div>
  <div class="col col--4">
     <ul>
       <li>新西兰</li>
       <li>波多黎各</li>
       <li>斯洛伐克</li>
       <li>英国</li>
       <li>美国</li>
     </ul>
  </div>
</div>

#### Moderately accurate travel times
#### 行程预估时间较为准确的国家或地区

Traffic data collected in these countries is less comprehensive. Travel times may be inaccurate, especially in heavy and unusual traffic conditions.

以下国家或地区所收集的交通路况数据不是很全面，所以由此估算出的行程时间也许不够准确，特别是在交通拥堵繁忙或者交通异常的情况下。

<div class="grid grid--gut24">
  <div class="col col--4">
    <ul>
      <li>Andorra</li>
      <li>Argentina</li>
      <li>Armenia</li>
      <li>Austria</li>
      <li>Barbados</li>
      <li>Belarus</li>
      <li>Belgium</li>
      <li>Belize</li>
      <li>Cayman Islands</li>
      <li>Chile</li>
      <li>Cyprus</li>
      <li>Denmark</li>
      <li>Dominican Republic</li>
    </ul>
  </div>
  <div class="col col--4">
    <ul>
      <li>Finland</li>
      <li>France</li>
      <li>Germany</li>
      <li>Guam</li>
      <li>Hungary</li>
      <li>Iran, Islamic Republic of</li>
      <li>Israel</li>
      <li>Italy</li>
      <li>Japan</li>
      <li>Jordan</li>
      <li>Lithuania</li>
      <li>Luxembourg</li>
      <li>Mexico</li>
    </ul>
  </div>
  <div class="col col--4">
  <ul>
    <li>Montenegro</li>
    <li>Norway</li>
    <li>Panama</li>
    <li>Paraguay</li>
    <li>Poland</li>
    <li>Portugal</li>
    <li>Romania</li>
    <li>Slovenia</li>
    <li>Spain</li>
    <li>Sweden</li>
    <li>Switzerland</li>
    <li>United Arab Emirates</li>
    <li>Uruguay</li>
  </ul>
  </div>
</div>

<div class="grid grid--gut24">
  <div class="col col--4">
    <ul>
      <li>安道尔</li>
      <li>阿根廷</li>
      <li>亚美尼亚</li>
      <li>奥地利</li>
      <li>巴巴多斯</li>
      <li>白俄罗斯</li>
      <li>比利时</li>
      <li>伯利兹</li>
      <li>开曼群岛</li>
      <li>智利</li>
      <li>塞浦路斯</li>
      <li>丹麦</li>
      <li>多米尼加共和国</li>
    </ul>
  </div>
  <div class="col col--4">
    <ul>
      <li>芬兰</li>
      <li>法国</li>
      <li>德国</li>
      <li>关岛</li>
      <li>匈牙利</li>
      <li>伊朗</li>
      <li>以色列</li>
      <li>意大利</li>
      <li>日本</li>
      <li>约旦</li>
      <li>立陶宛</li>
      <li>卢森堡</li>
      <li>墨西哥</li>
    </ul>
  </div>
  <div class="col col--4">
  <ul>
    <li>黑山</li>
    <li>挪威</li>
    <li>巴拿马</li>
    <li>巴拉圭</li>
    <li>波兰</li>
    <li>葡萄牙</li>
    <li>罗马尼亚</li>
    <li>斯洛文尼亚</li>
    <li>西班牙</li>
    <li>瑞典</li>
    <li>瑞士</li>
    <li>阿联酋</li>
    <li>乌拉圭</li>
  </ul>
  </div>
</div>

#### Limited predictability of travel times
#### 行程时间预估准确度极为有限的国家或地区

Traffic data collected in these countries is less comprehensive. Travel times are less reliable, with partial coverage of traffic conditions.

以下国家或地区所收集的交通路况数据不是很全面，只有覆盖到了部分交通状况，所以由此估算出的行程时间并不可靠。

<div class="grid grid--gut24">
  <div class="col col--4">
    <ul>
      <li>Åland Islands</li>
      <li>Albania</li>
      <li>Algeria</li>
      <li>American Samoa</li>
      <li>Anguilla</li>
      <li>Aruba</li>
      <li>Azerbaijan</li>
      <li>Bahamas</li>
      <li>Bahrain</li>
      <li>Bangladesh</li>
      <li>Bermuda</li>
      <li>Bolivia, Plurinational State of</li>
      <li>Bosnia and Herzegovina</li>
      <li>Botswana</li>
      <li>Brazil</li>
      <li>Brunei Darussalam</li>
      <li>Bulgaria</li>
      <li>Cambodia</li>
      <li>Cameroon</li>
      <li>Colombia</li>
      <li>Cook Islands</li>
      <li>Costa Rica</li>
      <li>Côte d'Ivoire</li>
      <li>Croatia</li>
      <li>Dominica</li>
      <li>Ecuador</li>
      <li>Egypt</li>
      <li>El Salvador</li>
      <li>Faroe Islands</li>
      <li>Fiji</li>
      <li>French Polynesia</li>
      <li>Georgia</li>
      <li>Ghana</li>
    </ul>
  </div>
  <div class="col col--4">
    <ul>
      <li>Greece</li>
      <li>Grenada</li>
      <li>Guatemala</li>
      <li>Haiti</li>
      <li>Holy See (Vatican City State)</li>
      <li>Honduras</li>
      <li>Hong Kong</li>
      <li>India</li>
      <li>Indonesia</li>
      <li>Iraq</li>
      <li>Jamaica</li>
      <li>Kazakhstan</li>
      <li>Kenya</li>
      <li>Kuwait</li>
      <li>Kyrgyzstan</li>
      <li>Laos</li>
      <li>Lebanon</li>
      <li>Macedonia, the Former Yugoslav Republic of</li>
      <li>Malawi</li>
      <li>Malaysia</li>
      <li>Malta</li>
      <li>Mauritius</li>
      <li>Moldova, Republic of</li>
      <li>Monaco</li>
      <li>Mongolia</li>
      <li>Morocco</li>
      <li>Myanmar</li>
      <li>Namibia</li>
      <li>Nepal</li>
      <li>New Caledonia</li>
      <li>Nicaragua</li>
      <li>Nigeria</li>
    </ul>
  </div>
  <div class="col col--4">
    <ul>
      <li>Oman</li>
      <li>Pakistan</li>
      <li>Philippines</li>
      <li>Qatar</li>
      <li>Russian Federation</li>
      <li>Saint Barthélemy</li>
      <li>Saint Kitts and Nevis</li>
      <li>Saint Lucia</li>
      <li>Saint Martin (French part)</li>
      <li>San Marino</li>
      <li>Saudi Arabia</li>
      <li>Senegal</li>
      <li>Serbia</li>
      <li>Sierra Leone</li>
      <li>Singapore</li>
      <li>Sint Maarten (Dutch part)</li>
      <li>South Africa</li>
      <li>Sri Lanka</li>
      <li>Swaziland</li>
      <li>Tajikistan</li>
      <li>Tanzania, United Republic of</li>
      <li>Thailand</li>
      <li>Trinidad and Tobago</li>
      <li>Tunisia</li>
      <li>Turkey</li>
      <li>Turks and Caicos Islands</li>
      <li>Ukraine</li>
      <li>Uzbekistan</li>
      <li>Venezuela, Bolivarian Republic of</li>
      <li>Viet Nam</li>
      <li>Virgin Islands, British</li>
      <li>Zambia</li>
    </ul>
  </div>
</div>

<div class="grid grid--gut24">
  <div class="col col--4">
    <ul>
      <li>奥兰群岛</li>
      <li>阿尔巴尼亚</li>
      <li>阿尔及利亚</li>
      <li>美属萨摩亚</li>
      <li>安圭拉</li>
      <li>阿鲁巴</li>
      <li>阿塞拜疆</li>
      <li>巴哈马</li>
      <li>巴林</li>
      <li>孟加拉国</li>
      <li>百慕大</li>
      <li>玻利维亚</li>
      <li>波斯尼亚和黑塞哥维那</li>
      <li>博茨瓦纳</li>
      <li>巴西</li>
      <li>文莱达鲁萨兰国</li>
      <li>保加利亚</li>
      <li>柬埔寨</li>
      <li>喀麦隆</li>
      <li>哥伦比亚</li>
      <li>库克群岛</li>
      <li>哥斯达黎加</li>
      <li>科特迪瓦</li>
      <li>克罗地亚</li>
      <li>多米尼克</li>
      <li>厄瓜多尔</li>
      <li>埃及</li>
      <li>萨尔瓦多</li>
      <li>法罗群岛</li>
      <li>斐济</li>
      <li>法属玻里尼西亚</li>
      <li>格鲁吉亚</li>
      <li>加纳</li>
    </ul>
  </div>
  <div class="col col--4">
    <ul>
      <li>希腊</li>
      <li>格林纳达</li>
      <li>危地马拉</li>
      <li>海地</li>
      <li>罗马教廷(梵蒂冈)</li>
      <li>洪都拉斯</li>
      <li>香港</li>
      <li>印度</li>
      <li>印度尼西亚</li>
      <li>伊拉克</li>
      <li>牙买加</li>
      <li>哈萨克斯坦</li>
      <li>肯尼亚</li>
      <li>科威特</li>
      <li>吉尔吉斯斯坦</li>
      <li>老挝</li>
      <li>黎巴嫩</li>
      <li>马其顿共和国</li>
      <li>马拉维</li>
      <li>马来西亚</li>
      <li>马耳他</li>
      <li>毛里求斯</li>
      <li>摩尔多瓦</li>
      <li>摩纳哥</li>
      <li>蒙古</li>
      <li>摩洛哥</li>
      <li>缅甸</li>
      <li>纳米比亚</li>
      <li>尼泊尔</li>
      <li>新喀里多尼亚</li>
      <li>尼加拉瓜</li>
      <li>尼日利亚</li>
    </ul>
  </div>
  <div class="col col--4">
    <ul>
      <li>阿曼</li>
      <li>巴基斯坦</li>
      <li>菲律宾</li>
      <li>卡塔尔</li>
      <li>俄罗斯联邦</li>
      <li>圣巴泰勒米</li>
      <li>圣基茨和尼维斯</li>
      <li>圣卢西亚</li>
      <li>圣马丁（法属）</li>
      <li>圣马力诺</li>
      <li>沙特阿拉伯</li>
      <li>塞内加尔</li>
      <li>塞尔维亚</li>
      <li>塞拉利昂</li>
      <li>新加坡</li>
      <li>圣马丁（荷属）</li>
      <li>南非</li>
      <li>斯里兰卡</li>
      <li>斯威士兰</li>
      <li>塔吉克斯坦</li>
      <li>坦桑尼亚</li>
      <li>泰国</li>
      <li>特立尼达和多巴哥</li>
      <li>突尼斯</li>
      <li>土耳其</li>
      <li>特克斯和凯科斯群岛</li>
      <li>乌克兰</li>
      <li>乌兹别克斯坦</li>
      <li>委内瑞拉玻利瓦尔共和国</li>
      <li>越南</li>
      <li>维尔京群岛（英属）</li>
      <li>赞比亚</li>
    </ul>
  </div>
</div>

## Using directions services
## 使用方向导航服务

There are many tools you can use to enable directions-related services for your Mapbox web or mobile application. You can access these services directly using the web services APIs, through our Navigation SDK, or using one of several plugins and libraries to integrate these services into applications across platforms.

我们有诸多工具供您选择，以帮助您添加方向导航相关服务到网页或者移动设备应用中。您可以通过使用网页服务web service APIs，我们的Navigation SDK来直接获取这些方向导航服务。或者您也可以选择使用插件和工具库来将这些服务导入到跨平台应用中去。

### Web services APIs
### 网页服务Web services APIs

There are four directions-related services that Mapbox offers:
Mapbox有四种方向导航相关服务API供您选择使用：

- **Mapbox Directions API**: retrieve point to point directions including a route with durations, estimated distances, and turn-by-turn instructions.
- **Mapbox Matrix API**: retrieve all travel times between many points.
- **Mapbox Optimization API**: retrieve duration-optimized trips between input coordinates.
- **Mapbox Map Matching API**: align existing fuzzy routes to the routeable network.

- **Mapbox Directions API**：获取点对点方向，包括一条带有预估行程时间，距离以及逐向道路导航指示信息的路径。
- **Mapbox Matrix API**：获取多地点间所有行程时间。
- **Mapbox Optimization API**：获取输入坐标点间预估时间最优行程路线。
- **Mapbox Map Matching API**：将现有模糊路径校准至可行合理的道路网络。

Read more about the required inputs for each API below.
您可以阅读下面的内容来了解每种API所需要的输入参数。

#### Mapbox Directions API

Requesting directions from the [Mapbox Directions API](https://docs.mapbox.com/api/navigation/#directions) requires at least two waypoints &mdash; an origin and a destination &mdash; but can also include up to 23 additional waypoints between (or a total of 25 waypoints). When making a request, you can specify additional parameters such as which **profile** to use (walking, cycling, driving, or driving with traffic) and whether alternate routes should be included in the response. You can also add optional annotations to your request, including duration, distance, speed, and congestion. We calculate congestion by comparing night-time traffic speeds with real-time traffic speeds and assign a level of congestion given the percentage difference.

从[Mapbox Directions API](https://docs.mapbox.com/api/navigation/#directions)请求方向导航服务需要至少两个航路点 &mdash; 一个起点和一个终点 &mdash; 但是也可以在起点与终点间额外包含最多23个航路点（换言之，总共最多25个航路点）。当要发出一个服务请求，您可以设定更多参数值，例如，将要使用哪一种**profile**（步行，骑行，驾驶，或考虑到路况的驾驶），是否在API返回信息中包含替换路线。您还可以在服务请求中添加自选注释，包括预估行程时间，距离，速度和交通拥堵状况。我们会通过对比夜间和实时行车速度来计算交通拥堵状况，并根据他们的差异百分比为实时交通状况指定一个交通拥堵等级。

You can read more about the directions response object in our [API documentation](https://docs.mapbox.com/api/navigation/#directions-response-object).

您可以通过阅读我们的[API Documentation](https://docs.mapbox.com/api/navigation/#directions-response-object)来了解更多方向导航服务返回对象相关的内容。

{{
  <DemoIframe src="https://www.mapbox.com/bites/00321/#14.48/38.9101/-77.0373" />
}}


#### Mapbox Matrix API

The Mapbox Matrix API returns travel times between many locations. Each location is either a source or destination. The number of sources and destinations are multiplied to create the matrix, or timetable, and calculate the number of [elements](/help/glossary/matrix-api-elements/) needed to make the request. For example, given three locations A, B, C, the Matrix API will return a matrix of all travel times in **seconds** between all locations:

Mapbox Matrix API可以为您返回多地点间的所有行程时间信息。每一个地点既是一个起点，也是一个终点。我们可以据此创建一个矩阵，或者时间表。它的[元素](https://docs.mapbox.com/help/glossary/matrix-api-elements/)数，或者说，每条服务请求需返回的元素数，由起点数量与终点数量的乘积决定。例如，假设我们有三个地点A，B，C，Matrix API将为我们返回一个以**秒**为单位，记录着所有地点间行程时间的矩阵，如下表：

|   | A     | B     | C     |
| - | ----- | ----- | ----- |
| A | A → A | A → B | A → C |
| B | B → A | B → B | B → C |
| C | C → A | C → B | C → C |

The Matrix API will always return the duration on the most efficient route for each location in the matrix, where an element is an origin-destination pair in the matrix.

Matrix API将无一例外地为您返回记录有最高效路线估算出的行程时间的矩阵时间表，而矩阵中的每一个元素对应一个起始-终点地点对。

Durations between locations may not be symmetric (for example A to B may have a different duration than B to A), as the routes may differ by direction due to one-way streets or turn restrictions. The Matrix API returns durations in seconds. It does not return route geometries or distances. Like the [Directions API](https://docs.mapbox.com/api/navigation/#directions), the Matrix API must be used on the same continent (does not cross over water bodies).

地点间预估行程时间在矩阵中不一定是对称的（例如，由A到B的预估行程时间可能不同于由B到A的预估行程时间），因为单行道和转弯限制可能会修改方向和路径选择。Matrix API返回的行程时间以秒为单位。它并不会返回路径几何及距离信息。如[Directions API](https://docs.mapbox.com/api/navigation/#directions)一样，Matrix API请求中的地点必须在同一块大陆上（不可跨越水体）。

This API allows you to build tools that efficiently check the reachability of coordinates from each other, filter locations by travel time, or run your own algorithms for solving optimization problems.

此API允许您构建可以高效检测坐标点间可达性的工具，根据行程时间筛选地点，以及使用您自己的算法解决优化问题。

Each request requires that you specify which locations are the **sources** and which are the **destinations**. There are four types of requests you can use with the Matrix API:

每一条服务请求需要您指定哪些地点为起点，哪些地点为终点。您在使用Matrix API时会有四种请求选择：

<div class='grid grid--gut24 mt24'>
  <div class='col col--7-ml col--12'>
    <p><strong>Many to many (NxN)</strong></p>

<div class='grid grid--gut24 mt24'>
  <div class='col col--7-ml col--12'>
    <p><strong>多对多 (NxN)</strong></p>

    <p>A many to many request is the default and generates the most results per-number-of-coordinates. This assumes all locations are sources and destinations. Without passing the source and destinations in the request, the response will still output an array of travel times between all locations.</p>
    
    <p>多对多请求是Matrix API的默认服务请求方式，它会为每一组选定坐标点生成最多查询结果。它假设每一个选定地点既为起点也为终点。即使我们不在请求指示中选定起点和终点，它仍将返回一组所有地点间完整的预估行程时间。</p>

    <pre><code>
    https://api.mapbox.com/directions-matrix/v1/mapbox/driving/A;B;C;D;E?access_token=....
    </code></pre>
  </div>
  <div class='col col--5-ml col--12'>
    <img alt="Many to Many (NxN)" src="/help/img/directions/matrix-nxn.png" />
  </div>
</div>

<div class='grid grid--gut24 mt24'>
  <div class='col col--7-ml col--12'>
    <p><strong>一对多 (1xN)</strong></p>

    <p>In some cases, you may have a single source (for example, one driver) and four potential destinations (four gas stations). To calculate which destination has the shortest drive time, you need to specify the source and destination coordinates.</p>
    
    <p>有些情况下, 您可能有一个起点（例如，一位驾驶司机只能从一个地点出发），多个潜在终点（例如，四个加油站）. 若要根据估算的驾驶时间找到最近的终点，您需要指定起点以及终点坐标。</p>

    <pre><code>
    https://api.mapbox.com/directions-matrix/v1/mapbox/driving/A;B;C;D;E?sources=0&destinations=1;2;3;4&access_token=...
    </code></pre>
  </div>
  <div class='col col--5-ml col--12'>
    <img alt="One to many (1xN)" src="/help/img/directions/matrix-1xn.png" />
  </div>
</div>

<div class='grid grid--gut24 mt24'>
  <div class='col col--7-ml col--12'>
    <p><strong>多对一 (Nx1)</strong></p>

    <p>An opposite request may be necessary where you may want to find travel times of many taxis to one rider, for example. In this case, a many to one (Nx1) request can be made by listing all your sources and your one destination.</p>
    
    <p>与一对多相反，还有一种服务请求可能需要您获取，比如，不同出租车司机前往一位顾客所在位置的行程时间。若是如此，您可以通过列举所有起点位置和终点位置来请求多对一（Nx1）行程时间估算服务。</p>

    <pre><code>
    https://api.mapbox.com/directions-matrix/v1/mapbox/driving/A;B;C;D;E?sources=1;2;3;4&destinations=0&access_token=....
    </code></pre>
  </div>
  <div class='col col--5-ml col--12'>
    <img alt="Many to one (Nx1)" src="/help/img/directions/matrix-nx1.png" />
  </div>
</div>

<div class='grid grid--gut24 mt24' markdown='1'>
  <div class='col col--7-ml col--12' markdown='1'>
    <p><strong>一些对一些 (MxN)</strong></p>

    <p>An uncommon but useful case is a several to several (NxM) request where you may want to be selective about how many sources and destinations you wish to find travel times to and from.</p>
    
    <p>还有一种不太常见但有用的情况是一些对一些(NxM)服务请求。您可以分别在完整的起点以及终点集合中选定您想要用来计算行程时间的几个起点和终点。</p>

    <pre><code>
    https://api.mapbox.com/directions-matrix/v1/mapbox/driving/A;B;C;D;E?sources=0;1&destinations=2;3;4&access_token=....
    </code></pre>
  </div>
  <div class='col col--5-ml col--12'>
    <img alt="Several to several NxM" src="/help/img/directions/matrix-nxm.png" />
  </div>
</div>

#### Mapbox Optimization API

The [Mapbox Optimization API](https://docs.mapbox.com/api/navigation/#optimization) returns a duration-optimized trip between the input coordinates. This is also known as solving the [Traveling Salesperson Problem](https://en.wikipedia.org/wiki/Travelling_salesman_problem). This API could be used to plan a route for deliveries in a city. Optimized trips can be retrieved for  driving, bicycling, and walking or hiking.

[Mapbox Optimization API](https://docs.mapbox.com/api/navigation/#optimization)为您返回输入坐标点间行程时间最优路径方案。这也可以被看作为著名的[旅行推销员问题](https://en.wikipedia.org/wiki/Travelling_salesman_problem)。这个API可以被用来计划城市配送行程方案。此外，它也能为驾车，骑行，步行甚至徒步旅行提供最优行程方案。

![optimized trips](/help/img/directions/routing.gif)


#### Mapbox Map Matching API

The [Mapbox Map Matching API](https://docs.mapbox.com/api/navigation/#map-matching) snaps fuzzy, inaccurate traces from a GPS unit or a phone to the OpenStreetMap road and path network using the Directions API. This produces clean paths that can be displayed on a map or used for other analysis.

[Mapbox Map Matching API](https://docs.mapbox.com/api/navigation/#map-matching)可以将GPS或手机设备中模糊、不准确的追踪路线校准至Directions API提供的OpenStreetMap道路网络上。它可以为地图显示及其他后续分析生成干净、准确的路径。

![map matching](https://i.imgur.com/j32cq1x.gif)

### Mapbox Navigation SDK

You can add directions to a mobile application using the [Mapbox Navigation SDK for iOS and Android](https://www.mapbox.com/navigation-sdk/). With the Navigation SDK, it takes only a few lines of code to display a complete navigation experience inside your app. With the Navigation SDK, you can:

您可以使用[Mapbox Navigation SDK for iOS and Android](https://www.mapbox.com/navigation-sdk/)将方向导航服务添加至移动设备应用中去。有了Navigation SDK，它只需短短几行代码就可以在您的应用中展示一个完整的导航体验。在Navigation SDK中，您可以：

- Generate turn-by-turn instructions.
- Use automatic rerouting when a user deviates from the route.
- Provide walking, biking, and driving directions.
- Provide real-time traffic.

- 生成逐向道路导航指示。
- 在用户偏离指定路线时自动变更导航路线。
- 为不同出行方式，如步行，骑行和驾驶，提供方向导航服务。
- 提供实时交通路况信息。

![navigation SDK](/help/img/directions/nav.png)

Read more about the Navigation SDK for [iOS](https://www.mapbox.com/ios-sdk/navigation/) and [Android](https://www.mapbox.com/android-docs/navigation/).

您可以通过阅读 [iOS](https://www.mapbox.com/ios-sdk/navigation/) 和 [Android](https://www.mapbox.com/android-docs/navigation/)来了解更多相关信息。

### Libraries and plugins
### 工具库和插件

We have several tools across platforms that allow you to integrate the Mapbox Directions API into your existing applications seamlessly:

我们有诸多跨平台工具供您将Mapbox Directions API服务无缝添加到现有应用中：

- **Web**: [Mapbox GL Directions plugin](https://www.mapbox.com/mapbox-gl-js/example/mapbox-gl-directions/) for Mapbox GL JS and [Mapbox-directions.js](https://www.mapbox.com/mapbox.js/plugins/) for Mapbox.js
- **Android**: [Mapbox Java SDK](https://www.mapbox.com/android-docs/java-sdk/examples/#directions)
- **iOS**: [MapboxDirections.swift](https://github.com/mapbox/MapboxDirections.swift)

- **Web网页**：为使用Mapbox GL JS， 我们有[Mapbox GL Directions插件](https://www.mapbox.com/mapbox-gl-js/example/mapbox-gl-directions/)；为使用Mapbox.js，我们有[Mapbox-directions.js插件](https://www.mapbox.com/mapbox.js/plugins/)。
- **Android**：我们有[Mapbox Java SDK](https://www.mapbox.com/android-docs/java-sdk/examples/#directions)。
- **iOS**: 我们有[MapboxDirections.swift](https://github.com/mapbox/MapboxDirections.swift)。

These tools allow you to add routing capabilities to your application, but have limitations when it comes to customization. These tools do not provide access to the Mapbox Matrix API, Optimization API, or Map Matching API. For more flexibility, you can [use our APIs directly](#web-services-apis).

您可以通过以上工具将路径导航服务添加至您的应用，但是这些工具在您自定义服务时会显示出功能上的限制。它们无法为您获取Mapbox Matrix API，Optimization API和Map Matching API服务。若您需要更加灵活全面的服务，请[直接使用我们的APIs](#web-services-apis)。

In addition, we offer libraries for:
此外，我们还为Python和JavaScript提供了以下工具库libraries：

- [Python](https://github.com/mapbox/mapbox-sdk-py)
- [JavaScript](https://github.com/mapbox/mapbox-sdk-js)

Here's an example of the [Mapbox GL Directions plugin](https://www.mapbox.com/mapbox-gl-js/example/mapbox-gl-directions/) in action:

这里有一个正在使用[Mapbox GL Directions插件](https://www.mapbox.com/mapbox-gl-js/example/mapbox-gl-directions/)的示例：

![Mapbox GL JS plugin](/help/img/directions/gljs-plugin.png)

### Testing the API
### 测试API服务

If you would like to get a feel for how the Mapbox Directions API works without building a whole application, we also provide an [API Playground](https://www.mapbox.com/api-playground/#/directions). Besides providing a convenient user interface to test queries, the API playground allows you to test the API's URL and query parameters, such as mode of transportation, steps, and alternative routes.

若您想在不构建一整个应用的情况下快速体验Mapbox Directions API服务，我们也为您提供了 [API Playground](https://www.mapbox.com/api-playground/#/directions)。除了具备方便测试查询请求的用户界面以外，这个API Playground还允许您测试API的请求URL网址和查询参数，例如，出行方式，是否包括具体导航步骤，是否包括替换路线信息等。

<a class='txt-bold' href='https://www.mapbox.com/api-playground/#/directions'>{{<ChevronousText text="Visit the API Playground" />}}</a>

## Providing directions feedback
## 为方向导航服务提供反馈意见

If you find issues with routing or any of the related services, you can provide feedback on our [Directions Feedback page](https://www.mapbox.com/feedback/). If you are using the API directly, you can use the [API Playground](https://www.mapbox.com/api-playground/#/directions?_k=8qemr9) to test your request, response, and any parameters you would like to include in your request.

如果您发现了任何有关路径及方向导航服务的问题，您可以将意见提交反馈给[方向导航服务反馈页面](https://www.mapbox.com/feedback/)。如果您正在使用API服务，您可以打开[API Playground](https://www.mapbox.com/api-playground/#/directions?_k=8qemr9)来测试您的服务请求，查看服务返回信息，以及指定任何其他想要包含在服务请求中的参数值。
