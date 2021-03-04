---
layout: post
title:  "공학연구인턴십_ShelterFinder-1"
date:   2020-06-07T14:25:52-05:00
navigation: True
tags: projects
subclass: 'post tag-windows'
logo: 'assets/images/ghost.png'
author: aka2344
disqus: true
---

#### 서론

작년 12월 말~올해 2월 초(19.12~20.02) 까지 학교에서 개설된 실습인 공학연구 인턴십에 참여하게 되었다. 학과의 각 연구실별로 학생들이 방학동안 연구 주제를 가지고 연구를 진행하는 프로그램으로, 나의 경우 공간데이터베이스 연구실에서 이 프로그램을 두 달간 진행하였다.

공간데이터연구실에서 진행중인 연구 중 공간에 대한 지형, 위치, 높이 등과 같은 공간정보를 토대로 공간데이터베이스를 활용하여, 해당 공간에 위치한 사람들에게 긴급 상황이 발생하였을 때 가장 빠르게 탈출할 수 있는 대피 경로를 안내해 주는 연구가 있다. 연구는 실내 공간을 대상으로 실내 재실자들에게 화재 발생 시 최적의 대피경로를 제공하는 목적으로 진행중에 있다. 

해당 연구를 참고하여, 나는 최근 우리나라에서 발생한 지진 사례들을 보고 사람들이 지진 발생 시 대피소까지 빠르게 대피해야 하는 상황이 발생하였을 때, 현재 위치에서 가장 가까운 대피소까지 대피할 수 있는 가장 빠르고 안전한 대피 경로를 찾아주면 어떨까라는 생각을 해보았다. 이러한 생각이 프로그램 또는 어플리케이션 등으로 구현된다면, 지진이 발생하였을 때 당황하지 않고 신속히 인근 대피소로 빠르게 대피하여 지진 상황을 신속하고 안전하게 대응할 수 있을 것이라고 생각하였고 이에 대한 연구 및 개발을 시작하기로 결정했다.



#### 설계

먼저 우리나라에 존재하는 지진 대피소에 대한 위치정보가 필요하다. 이를 위해 전국 지진해일 대피소 엑셀파일을 다운받았고, 이를 공간데이터 형태인 shapefile로 변환하였다. 또한, 최단 경로 분석을 위한 노드, 엣지로 구성된 데이터가 필요한데, 이는 OpenStreetMap에서 도로 데이터를 osm 파일 형식으로 추출하여 PostgreSQL의 Postgis를 통한 공간데이터 형태로 구축하였다. 본 연구에서는 범위를 서울시 영역으로 한정하여 진행하였다.

최단 경로 분석을 위해 다양한 지도 관련 기능을 제공하는 Mapbox gl js를 통해 기본적인 지도 기능을 구현하고,  html에서 자바스크립트를 통해 실행되도록 설계하였다. 대피소를 포함한 추가적인 공간데이터는 Geoserver를 통해 개발자의 로컬 서버에서 공간데이터를 업로드하여 Mapbox상에서 이를 불러와 사용할 수 있도록 설계하였다.

 또한, 최단경로 분석은 PostgreSQL의 PostGIS가 제공하는 기능들을 통해 사용자의 현재 위치에서 거리상으로 가장 가까운 대피소 찾기와 해당 대피소까지 대피하는 최단경로 계산 기능을 수행하도록 설계하였다. 이러한 작업은 PostgreSQL의 데이터베이스상에서 이루어지는데, 앞서 설계한 Mapbox의 지도와의 연계를 위해 자바를 통한 DB연동을 구축하여 Servlet을 통해 서버 연동으로 이를 수행하도록 설계하였다.

종합적으로, 본 연구에 사용한 개발 도구들은 다음과 같다.

- Mapbox GL js
- GeoServer
- PostgreSQL(PostGIS)
- Java(sevlet)



#### 개발 과정

앞서 설명하였듯이, 오픈 소스인 OpenStreetMap의 데이터를 이용하여 최단분석에 필요한 노드-링크 형태의 그래프 데이터를 공간데이터베이스 상에 구축하여야 한다. 기본적으로 OpenStreetMap에는 지리상으로 존재하는 도로, 인도 데이터가 포함되어 있으므로 이를 추출하여 활용할 수 있다.

이는 PostgreSQL의 Postgis 기능 중 하나인 osm2pgrouting을 사용하여 osm파일을 경로분석이 가능한 pgrouting의 형태로 데이터베이스에 저장하도록 실행한다. 아래 사진은 osm2pgrouting 기능에 대한 각각의 옵션과 설명이다. osm2pgrouting에 대한 자세한 설명은 [링크](https://github.com/pgRouting/osm2pgrouting/wiki/Documentation-for-osm2pgrouting-v2.2) 를 통해 확인할 수 있다.

![OSM2PGROUTING](/assets/OSM2PGROUTING.PNG)

이를 실행하여 osm 데이터를 변환하면, PostgreSQL 데이터베이스에는 도로에 대한 노드, 엣지 데이터가 추출되어 아래와 같이 저장된다. 

![ways](/assets/ways.PNG)

![node](/assets/node.PNG)

첫번째 사진은 ways 테이블로 각 도로들에 대한 주소, 좌표, 이름, 노드, 길이,  비용 등이 저장되어 있다. 두번째 사진은 ways_vertices_pgr 테이블로 각각의 노드들에 대한 주소와 좌표가 저장되어 있다.

이렇게 구축된 노드-링크 형태의 데이터를 gis tool(QGIS)를 통해 지도상에 표현하면 다음과 같이 나타난다.

![노드엣지](/assets/nodeedge.PNG)

따라서 이렇게 구축된 데이터를 통해 경로 분석이 가능한데, 특히 ways 테이블에 저장된 비용(cost) 항목을 통해 최단경로를 계산하는 알고리즘을 사용할 수 있다. 이는, 각 도로의 길이에 비례한 항목으로 도로가 길면 높은 비용을, 도로가 짧으면 낮은 비용이 저장되며 이를 통해 그래프 알고리즘에서 최단경로 계산에 사용되는 항목이 된다.



다음으로, 지진 대피소에 대한 데이터를 구축한다.  먼저, 공공데이터 포털에서 전국 지진해일 대피소가 있는 엑셀 파일을 다운받고, 서울시에 대한 대피소만 나누어 저장한다. 

![shelter](/assets/shelter.PNG)

대피소 엑셀 파일에는 이름, 주소, 좌표, 수용인원, 관리기관 등에 대한 항목들이 저장되어 있는데, 여기서 좌표에 해당하는 위도와 경도값을 통해 지리상의 공간데이터로 구축이 가능하다. 위 파일을 csv로 변환하고, QGIS에 로드하면 각 대피소들이 좌표에 대응하는 위치에 표시가 되고, 이를 다시 공간데이터 형태인 shp파일로 저장한다. 위의 qgis 화면에 나와있는 shelter 포인트들이 각각의 대피소 데이터이다. 

대피소 데이터의 경우, 경로 분석 뿐만 아니라 사용자의 지도상에서도 출력되어 각각의 위치를 확인할 수 있어야 하므로, Mapbox상의 지도에 표현되어야 한다. 앞서 저장한 대피소의 shp파일을 공간적인 json 형태인 geojson 파일로 저장하여 mapbox에 로드하여 정적 파일로서 사용할 수 있으나, 대피소의 경우 새롭게 추가되거나 기존 대피소가 폐쇄되는 경우가 발생할 경우, 이를 반영하여 긴급 상황에서도 활용하는데 한계가 존재하기 때문에, 서버에서 불러오는 형태로 데이터가 저장되어야 한다.

따라서, 이를 위해 별도의 서버를 구축해야 하는데, 이는 GeoServer를 통해 구현이 가능하다.

![geoserver](/assets/geoserver.PNG)

위 화면은 GeoServer의 메인 페이지 화면으로, 설치를 마치고 서버를 시작하면 위와 같은 메인페이지로 접속이 가능하며, 데이터 항목에서 공간데이터를 업로드 할 수 있다. 이렇게 서버에 업로드한 데이터를 WMS(World Map Service)형태로 Export하여 외부에서 사용할 수 있는데, Mapbox에서 이러한 형태로 대피소 데이터를 서버에 업로드하고 이를 불러와서 사용할 수 있다.

![mapbox map](/assets/mapbox map.PNG)

Mapbox API를 이용하여 기본적인 지도와 대피소 데이터를 구축하면 위와 같은 화면으로 표시된다. 이것은 HTML 소스를 통해 구현한 화면으로, 자바스크립트를 통해 mapbox의 지도 기능과 wms 를 통한 외부 데이터(대피소) 출력을 구현하였다.

```javascript
map.addSource('shelter',{

'type': 'geojson',

'data': 'http://localhost:8080/geoserver/test1/ows?service=WFS&version=1.0.0&request=GetFeature&typeName=test1:shel_seoul&maxFeatures=3000&outputFormat=application/json'

})

map.addLayer(

{

'id': 'points',

'type': 'symbol',

'source': 'shelter',

'layout': {

// get the icon name from the source's "icon" property

// concatenate the name to get an icon from the style's sprite sheet

// get the title name from the source's "title" property

"icon-image": "marker-15",

"icon-size": 3

}} 

);
```



위 코드는 스크립트 코드 중 대피소 데이터를 지도에 추가하는 부분인데, 코드에서 확인할 수 있듯이 wms 주소를 입력하여 해당 대피소 데이터를 mapbox 상에서의 layer 형태로 추가하는 것을 볼 수 있다. 따라서 추가된 대피소 데이터들은 마커의 형태로 지도 위에 출력된다.