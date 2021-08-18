---
title: Microsoft Azure Maps 中的天气服务概念
description: 了解适用于 Microsoft Azure Maps 天气服务的概念。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: b123caf65c86980dfa3cacf5e7d412851332afc8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747777"
---
# <a name="weather-services-in-azure-maps"></a>Azure Maps 中的天气服务

本文介绍适用于 Azure Maps [天气服务](/rest/api/maps/weather)的概念。 建议在开始使用天气 API 之前通读本文。

## <a name="unit-types"></a>单位类型

一些天气服务（预览版）API 允许用户指定是以公制单位还是以英制单位返回数据。 这些 API 返回的响应包括 unitType 和可用于单位转换的数值。 请参阅下表来解释这些值。

|unitType|说明         |
|--------|--------------------|
|0       |英尺                |
|1       |英寸              |
|2       |英里               |
|3       |毫米          |
|4       |厘米          |
|5       |米               |
|6       |千米           |
|7       |公里每小时   |
|8       |节               |
|9       |英里每小时        |
|10      |米每秒     |
|11      |百帕        |
|12      |英寸汞柱     |
|13      |千帕         |
|14      |毫巴           |
|15      |毫米汞柱|
|16      |每平方英寸磅数 |
|17      |摄氏温度             |
|18      |华氏温度          |
|19      |开氏温度              |
|20      |%             |
|21      |FLOAT               |
|22      |integer             |
|31      |MicrogramsPerCubicMeterOfAir             |

## <a name="weather-icons"></a>天气图标

某些天气服务（预览版）API 在响应中返回 `iconCode`。 `iconCode` 是用于定义图标的数值。 请勿直接从应用程序链接到这些图像，因为 URL 可能会改变。

| 图标编号 |图标| 天 | 夜间 | 文本 |
|-------------|:----:|-----|-------|------|
| 1           | :::image type="icon" source="./media/weather-services-concepts/sunny-i.png"::: | 是 |  否    | 晴|
| 2           | :::image type="icon" source="./media/weather-services-concepts/mostly-sunny.png"::: | 是 |  否    | 晴时多云|
| 3           | :::image type="icon" source="./media/weather-services-concepts/partly-sunny.png"::: | 是 |  否    | 局部晴朗|
| 4           | :::image type="icon" source="./media/weather-services-concepts/intermittent-clouds.png"::: | 是 |  否    | 间歇性多云|
| 5           | :::image type="icon" source="./media/weather-services-concepts/hazy-sunshine.png"::: | 是 |  否    | 朦胧的阳光 |
| 6           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy.png"::: | 是 |  否    | 大部多云|
| 7           | :::image type="icon" source="./media/weather-services-concepts/cloudy-i.png"::: | 是 |  是   | 多云 |
| 8           | :::image type="icon" source="./media/weather-services-concepts/dreary-overcast.png"::: | 是 |  是   | 阴沉（阴）|
| 11           | :::image type="icon" source="./media/weather-services-concepts/fog-i.png"::: | 是 |  是   | 雾|
| 12           | :::image type="icon" source="./media/weather-services-concepts/showers-i.png"::: | 是 |  是   | 阵雨|
| 13           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-showers.png"::: | 是 |  否    | 大部多云有阵雨|
| 14           | :::image type="icon" source="./media/weather-services-concepts/partly-sunny-showers.png"::: | 是 |  否    | 局部晴朗有阵雨|
| 15           | :::image type="icon" source="./media/weather-services-concepts/tstorms-i.png"::: | 是 |  是   | 雷暴|
| 16           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-tstorms.png"::: | 是 |  否    | 大部多云有雷暴|
| 17           | :::image type="icon" source="./media/weather-services-concepts/partly-sunny-tstorms.png"::: | 是 |  否    | 局部晴朗有雷暴|
| 18           | :::image type="icon" source="./media/weather-services-concepts/rain-i.png"::: | 是 |  是   | 雨|
| 19           | :::image type="icon" source="./media/weather-services-concepts/flurries-i.png"::: | 是 |  是   | 小雪|
| 20           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-flurries.png"::: | 是 |  否    | 大部多云有小雪|
| 21           | :::image type="icon" source="./media/weather-services-concepts/partly-sunny-flurries.png"::: | 是 |  否    | 局部晴朗有小雪|
| 22           | :::image type="icon" source="./media/weather-services-concepts/snow-i.png"::: | 是 |  是   | 雪|
| 23           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-snow.png"::: | 是 |  否    | 大部多云有雪|     
| 24           | :::image type="icon" source="./media/weather-services-concepts/ice-i.png"::: | 是 |  是   | Ice |
| 25           | :::image type="icon" source="./media/weather-services-concepts/sleet-i.png"::: | 是 |  是   | 雨夹雪|
| 26           | :::image type="icon" source="./media/weather-services-concepts/freezing-rain.png"::: | 是 |  是   | 冻雨|
| 29           | :::image type="icon" source="./media/weather-services-concepts/rain-snow.png"::: | 是 |  是   | 雨夹雪|
| 30           | :::image type="icon" source="./media/weather-services-concepts/hot-i.png"::: | 是 |  是   | 热|
| 31           | :::image type="icon" source="./media/weather-services-concepts/cold-i.png"::: | 是 |  是   | 冷|
| 32           | :::image type="icon" source="./media/weather-services-concepts/windy-i.png"::: | 是 |  是   | Windy|
| 33           | :::image type="icon" source="./media/weather-services-concepts/clear-night.png"::: | 否  |  是   | 清除|
| 34           | :::image type="icon" source="./media/weather-services-concepts/mostly-clear-night.png"::: | 否  |  是   | 大部晴朗|
| 35           | :::image type="icon" source="./media/weather-services-concepts/partly-cloudy-night.png"::: | 否  |  是   | 局部多云|
| 36           | :::image type="icon" source="./media/weather-services-concepts/intermittent-clouds-Night.png"::: | 否  |  是   | 间歇性多云|
| 37           | :::image type="icon" source="./media/weather-services-concepts/hazymoon-light.png"::: | 否  |  是   | 朦胧的月光|
| 38           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-night.png"::: | 否  |  是   | 大部多云|
| 39           | :::image type="icon" source="./media/weather-services-concepts/partly-cloudy-showers-night.png"::: | 否  |  是   | 局部多云有阵雨|
| 40           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-showers-night.png"::: | 否  |  是   | 大部多云有阵雨|
| 41           | :::image type="icon" source="./media/weather-services-concepts/partly-cloudy-tstorms-night.png"::: | 否  |  是   | 局部多云有雷暴|
| 42           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-tstorms-night.png"::: | 否  |  是   | 大部多云有雷暴|
| 43           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-flurries-night.png"::: | 否  |  是   | 大部多云有小雪|
| 44           | :::image type="icon" source="./media/weather-services-concepts/mostly-cloudy-snow.png"::: | 否  |  是   | 大部多云有雪|


## <a name="radar-and-satellite-imagery-color-scale"></a>雷达和卫星图像色阶

通过[获取地图图块 v2 API](/rest/api/maps/render-v2/get-map-tile)，用户可以请求最新的雷达图像和红外卫星图像。 请参阅以下指南，以帮助解释用于雷达和卫星图块的颜色。

### <a name="radar-images"></a>雷达图像

下表提供解释雷达图像以及为雷达图块数据创建地图图例的指导。

| 十六进制颜色代码 | 颜色示例 | 天气情况 |
|----------------|--------------|-------------------|
| #93c701        | ![小雨的颜色。](./media/weather-services-concepts/color-93c701.png) | 小雨 |
| #ffd701        | ![中雨的颜色。](./media/weather-services-concepts/color-ffd701.png) | 中雨 |
| #f05514        | ![大雨的颜色。](./media/weather-services-concepts/color-f05514.png) | 大雨 |
| #dc250e        | ![暴雨的颜色。](./media/weather-services-concepts/color-dc250e.png) | 暴雨 |
| #9ec8f2        | ![小雪的颜色。](./media/weather-services-concepts/color-9ec8f2.png) | 小雪 |
| #2a8fdb        | ![中雪的颜色。](./media/weather-services-concepts/color-2a8fdb.png) | 中雪 |
| #144bed        | ![大雪的颜色。](./media/weather-services-concepts/color-144bed.png) | 大雪 |
| #020096        | ![暴雪的颜色。](./media/weather-services-concepts/color-020096.png) | 暴雪 |
| #e6a5c8        | ![薄冰的颜色。](./media/weather-services-concepts/color-e6a5c8.png) | 薄冰 |
| #d24fa0        | ![中冰的颜色。](./media/weather-services-concepts/color-d24fa0.png) | 中冰 |
| #b71691        | ![大冰的颜色。](./media/weather-services-concepts/color-b71691.png) | 大冰 |
| #7a1570        | ![暴冰的颜色。](./media/weather-services-concepts/color-7a1570.png) | 暴冰 |
| #c196e6        | ![轻度混合的颜色。](./media/weather-services-concepts/color-c196e6.png) | 轻度混合 |
| #ae6ee6        | ![中度混合的颜色。](./media/weather-services-concepts/color-ae6ee6.png) | 中度混合 |
| #8a32d7        | ![重度混合的颜色。](./media/weather-services-concepts/color-8a32d7.png) | 重度混合 |
| #6500ba        | ![严重混合的颜色。](./media/weather-services-concepts/color-6500ba.png) | 严重混合 |

下面显示了带有十六进制颜色代码和 dBZ 值的雷达图块的详细调色板。 dBZ 表示天气雷达图中的降水强度。 

| **雨**             | **冰**              | **雪**              | **MIXED**             |
|----------------------|----------------------|-----------------------|-----------------------|
| **dBZ** **(color)**  | **dBZ** **(color)**  | **dBZ** **(color)**   | **dBZ** **(color)**   |
| 1.25       (#93C701) | 1.25       (#E6A5C8) | 1.25       (#9EC8F2)  | 1.25        (#C196E6) |
| 2.5        (#92C201) | 2.5        (#E6A2C6) | 2.5        (#98C5F0)  | 2.5         (#BF92E6) |
| 3.75       (#92BE01) | 3.75       (#E69FC5) | 3.75       (#93C3EF)  | 3.75        (#BD8EE6) |
| 5          (#92BA02) | 5          (#E69DC4) | 5          (#8DC1EE)  | 5           (#BB8BE6) |
| 6.25       (#92B502) | 6.25       (#E69AC2) | 6.25       (#88BFEC)  | 6.25        (#BA87E6) |
| 6.75       (#92B403) | 7.5       (#E697C1)  | 7.5        (#82BDEB)   | 7.5         (#B883E6) |
| 8          (#80AD02) | 8.75       (#E695C0) | 8.75       (#7DBAEA)  | 8.75        (#B680E6) |
| 9.25       (#6FA602) | 10         (#E692BE) | 10         (#77B8E8)  | 10          (#B47CE6) |
| 10.5       (#5EA002) | 11.25      (#E68FBD) | 11.25      (#72B6E7)  | 11.25       (#B378E6) |
| 11.75      (#4D9902) | 12.5       (#E68DBC) | 12.5       (#6CB4E6)  | 12.5        (#B175E6) |
| 12.25      (#479702) | 13.75      (#E68ABA) | 13.75      (#67B2E5)  | 13.75       (#AF71E6) |
| 13.5       (#3D9202) | 15         (#E687B9) | 15         (#61AEE4)  | 15          (#AE6EE6) |
| 14.75      (#338D02) | 16.25      (#E685B8) | 16.25      (#5BABE3)  | 16.25       (#AB6AE4) |
| 16         (#298802)  | 17.5       (#E682B6) | 17.5       (#56A8E2)  | 17.5        (#A967E3) |
| 17.25      (#1F8302) | 18.75      (#E67FB5) | 18.75      (#50A5E1)  | 18.75       (#A764E2) |
| 17.75      (#1B8103) | 20         (#E67DB4) | 20         (#4BA2E0)  | 20          (#A560E1) |
| 19         (#187102) | 21.25      (#E275B0) | 21.25       (#459EDF) | 21.25       (#A35DE0) |
| 20.25      (#166102) | 22.5       (#DF6DAD) | 22.5       (#409BDE)  | 22.5        (#A15ADF) |
| 20.75      (#165B02) | 23.75      (#DC66AA) | 23.75      (#3A98DD)  | 23.75       (#9F56DE) |
| 22         (#135001) | 25         (#D85EA6) | 25         (#3595DC)  | 25          (#9D53DD) |
| 23.25      (#114501) | 26.25      (#D556A3) | 26.25      (#2F92DB)  | 26.25       (#9B50DC) |
| 24.5       (#0F3A01) | 27.5       (#D24FA0) | 27.5       (#2A8FDB)  | 27.5        (#9648DA) |
| 25.75      (#124C01) | 28.75      (#CE479E) | 28.75      (#2581DE)  | 28.75       (#9241D9) |
| 27         (#114401) | 30         (#CB409C) | 30         (#2173E2)  | 30          (#8E39D8) |
| 28.25      (#0F3D01) | 31.25      (#C7399A) | 31.25      (#1C66E5)  | 31.25       (#8A32D7) |
| 28.75      (#0F3A01) | 32.5       (#C43298) | 32.5       (#1858E9)  | 32.5        (#862ED2) |
| 30         (#375401) | 33.75      (#C12B96) | 33.75      (#144BED)  | 33.75       (#832BCE) |
| 31.25      (#5F6E01) | 35         (#BD2494) | 35         (#1348EA)  | 35          (#7F28C9) |
| 32.5       (#878801) | 36.25      (#BA1D92) | 36.25      (#1246E7)  | 36.25       (#7C25C5) |
| 33.75      (#AFA201) | 37.5       (#B71691) | 37.5       (#1144E4)  | 37.5        (#7822C1) |
| 35         (#D7BC01) | 38.75      (#B51690) | 38.75      (#1142E1)  | 38.75       (#751FBC) |
| 36.25      (#FFD701) | 40         (#B3168F) | 40         (#1040DE)  | 40          (#711CB8) |
| 37.5       (#FEB805) | 41.25      (#B1168E) | 41.25      (#0F3EDB)  | 41.25       (#6E19B4) |
| 38.75      (#FCAB06) | 42.5       (#AF168D) | 42.5       (#0F3CD8)  | 42.5        (#6D18B4) |
| 40         (#FA9E07) | 43.75      (#AD168C) | 43.75      (#0E3AD5)  | 43.75       (#6D17B4) |
| 41.25      (#F89209) | 45         (#AB168B) | 45         (#0D38D2)  | 45          (#6D16B4) |
| 42.5       (#F05514) | 46.25      (#A9168A) | 46.25      (#0C36CF)  | 46.25       (#6C15B4) |
| 43.75      (#E74111) | 47.5       (#A81689) | 47.5       (#0C34CC)  | 47.5        (#6C14B5) |
| 45         (#DF2D0F) | 48.75      (#A61688) | 48.75      (#0B32C9)  | 48.75       (#6C13B5) |
| 45.5       (#DC250E) | 50         (#A41687) | 50         (#0A30C6)  | 50          (#6B12B5) |
| 46.75      (#D21C0C) | 51.25      (#A21686) | 51.25      (#0A2EC4)  | 51.25       (#6B11B5) |
| 48         (#C9140A) | 52.5       (#A01685) | 52.5       (#092BC1)  | 52.5        (#6B10B6) |
| 49.25      (#BF0C09) | 53.75      (#9E1684) | 53.75      (#0929BF)  | 53.75       (#6A0FB6) |
| 50         (#BA0808) | 55         (#9C1683) | 55         (#0826BC)  | 55          (#6A0EB6) |
| 56.25      (#6f031b) | 56.25      (#9B1682) | 56.25      (#0824BA)  | 56.25       (#6A0DB6) |
| 57.5       (#9f0143) | 57.5       (#981580) | 57.5       (#0721B7)  | 57.5        (#690CB6) |
| 58.75      (#c10060) | 58.75      (#96157F) | 58.75      (#071FB5)  | 58.75       (#690CB7) |
| 60         (#e70086) | 60         (#94157E) | 60         (#071DB3)  | 60          (#690BB7) |
| 61.25      (#e205a0) | 61.25      (#92157D) | 61.25      (#061AB0)  | 61.25       (#680AB7) |
| 62.5       (#cc09ac) | 62.5       (#90157C) | 62.5       (#0618AE)  | 62.5        (#6809B7) |
| 63.75      (#b50eb7) | 63.75      (#8D157A) | 63.75      (#0515AB)  | 63.75       (#6808B8) |
| 65         (#9315c8) | 65         (#8B1579) | 65         (#0513A9)  | 65          (#6707B8) |
| 66.25      (#8f21cc) | 66.25      (#891578) | 66.25      (#0410A6)  | 66.25       (#6706B8) |
| 67.5       (#983acb) | 67.5       (#871577) | 67.5       (#040EA4)  | 67.5        (#6705B8) |
| 68.75      (#9d49cb) | 68.75      (#851576) | 68.75      (#040CA2)  | 68.75       (#6604B8) |
| 70         (#a661ca) | 70         (#821574) | 70         (#03099F)  | 70          (#6603B9) |
| 71.25      (#ad72c9) | 71.25      (#801573) | 71.25      (#03079D)  | 71.25       (#6602B9) |
| 72.5       (#b78bc6) | 72.5       (#7E1572) | 72.5       (#02049A)  | 72.5        (#6501B9) |
| 73.75      (#bf9bc4) | 73.75      (#7C1571) | 73.75      (#020298)  | 73.75       (#6500B9) |
| 75         (#c9b5c2) | 75         (#7A1570) | 75         (#020096)  | 75          (#6500BA) |



### <a name="satellite-images"></a>卫星图像

下表提供以下方面的指导：解释显示云温度的红外卫星图像以及为这些图块创建地图图例。 

| 十六进制颜色代码 | 颜色示例 | 云温 |
|----------------|--------------|-------------------|
| #b5b5b5        | ![#b5b5b5 的颜色图块。](./media/weather-services-concepts/color-b5b5b5.png) | 温度低 | 
| #d24fa0        | ![#d24fa0 的颜色图块。](./media/weather-services-concepts/color-d24fa0.png) |  |
| #8a32d7        | ![#8a32d7 的颜色图块。](./media/weather-services-concepts/color-8a32d7.png) |  |
| #144bed        | ![#144bed 的颜色图块。](./media/weather-services-concepts/color-144bed.png) |  |
| #479702        | ![#479702 的颜色图块。](./media/weather-services-concepts/color-479702.png) |  |
| #72b403        | ![#72b403 的颜色图块。](./media/weather-services-concepts/color-72b403.png) |  |
| #93c701        | ![#93c701 的颜色图块。](./media/weather-services-concepts/color-93c701.png) |  |
| #ffd701        | ![#ffd701 的颜色图块。](./media/weather-services-concepts/color-ffd701.png) |  |
| #f05514        | ![#f05514 的颜色图块。](./media/weather-services-concepts/color-f05514.png) |  |
| #dc250e        | ![#dc250e 的颜色图块。](./media/weather-services-concepts/color-dc250e.png) |  |
| #ba0808        | ![#ba0808 的颜色图块。](./media/weather-services-concepts/color-ba0808.png) |  |
| #1f1f1f        | ![#1f1f1f 的颜色图块。](./media/weather-services-concepts/color-1f1f1f.png) | 温度高 |


下面显示了红外卫星图块的详细调色板。

|**温度 (K)**|**十六进制颜色代码**|
|--------|--------------|
|198     |#fe050505     |
|198.43  |#fe120505     |
|198.87  |#fc1f0505     |
|199.3   |#fc2c0606     |
|199.74  |#fa390606     |
|200.17  |#fa460606     |
|200.61  |#f8530606     |
|201.04  |#f8600707     |
|201.48  |#f66c0707     |
|201.91  |#f6790707     |
|202.35  |#f4860707     |
|202.78  |#f4930707     |
|203.22  |#f2a00808     |
|203.65  |#f2ad0808     |
|204.09  |#f0ba0808     |
|204.52  |#f0bd0a09     |
|204.96  |#eec00d09     |
|205.39  |#eec30f0a     |
|205.83  |#ecc5120a     |
|206.26  |#ecc8140b     |
|206.7   |#eacb170b     |
|207.13  |#eace190c     |
|207.57  |#e8d11b0c     |
|208     |#e8d41e0d     |
|208.43  |#e6d6200d     |
|208.87  |#e6d9230e     |
|209.3   |#e4dc250e     |
|209.74  |#e4df2c0f     |
|210.17  |#e2e23310     |
|210.61  |#e2e53a11     |
|211.04  |#e0e73a11     |
|211.48  |#e0ea4712     |
|211.91  |#deed4e13     |
|212.35  |#def05514     |
|212.78  |#dcf15e13     |
|213.22  |#dcf26811     |
|213.65  |#daf37110     |
|214.09  |#daf47a0f     |
|214.52  |#d8f5830d     |
|214.96  |#d8f68d0c     |
|215.39  |#d6f8960b     |
|215.83  |#d6f99f09     |
|216.26  |#d4faa908     |
|216.7   |#d4fbb206     |
|217.13  |#d2fcbb05     |
|217.57  |#d2fdc404     |
|218     |#d0fece02     |
|218.43  |#d0ffd701     |
|218.87  |#cef8d601     |
|219.3   |#cef2d501     |
|219.74  |#ccebd401     |
|220.17  |#cce4d301     |
|220.61  |#caddd201     |
|221.04  |#cad7d101     |
|221.48  |#c8d0d001     |
|221.91  |#c8c9cf01     |
|222.35  |#c6c2ce01     |
|222.78  |#c6bccd01     |
|223.22  |#c4b5cc01     |
|223.65  |#c4aecb01     |
|224.09  |#c2a7ca01     |
|224.52  |#c2a1c901     |
|224.96  |#c09ac801     |
|225.39  |#c093c701     |
|225.83  |#be90c501     |
|226.26  |#be8ec401     |
|226.7   |#bc8bc202     |
|227.13  |#bc88c102     |
|227.57  |#ba85bf02     |
|228     |#ba83be02     |
|228.43  |#b880bc02     |
|228.87  |#b87dba02     |
|229.3   |#b678b703     |
|229.74  |#b675b603     |
|230.17  |#b472b403     |
|230.61  |#b46eb203     |
|231.04  |#b26bb203     |
|231.48  |#b267ad03     |
|231.97  |#b064aa03     |
|232.35  |#b060a803     |
|232.78  |#ae5da603     |
|233.22  |#ae59a302     |
|233.65  |#ac55a102     |
|234.09  |#ac529e02     |
|234.52  |#aa4e9c02     |
|234.96  |#aa4b9902     |
|235.39  |#a8479702     |
|235.83  |#a845940b     |
|236.26  |#a6439115     |
|236.7   |#a6418e1e     |
|237.13  |#a43f8b28     |
|237.57  |#a43d8831     |
|238     |#a23b853a     |
|238.43  |#a2398244     |
|238.87  |#a0377f4d     |
|239.3   |#a0357c57     |
|239.74  |#9e337960     |
|240.17  |#9e317669     |
|240.61  |#9c2f7373     |
|241.04  |#9c2c6f7c     |
|241.48  |#9a2a6c86     |
|241.91  |#9a28698f     |
|242.35  |#98266698     |
|242.78  |#982463a2     |
|243.22  |#962260ab     |
|243.65  |#96205db5     |
|244.09  |#941e5abe     |
|244.52  |#941c57c7     |
|244.85  |#921a54d1     |
|245.39  |#921851da     |
|245.83  |#90164ee4     |
|246.26  |#90144bed     |
|246.7   |#8e2148eb     |
|247.13  |#8e2e45e8     |
|247.57  |#8c3b43e6     |
|248     |#8c4840e3     |
|248.43  |#8a563de1     |
|248.87  |#8a633ade     |
|249.3   |#887038dc     |
|249.74  |#887d35d9     |
|250.17  |#868a32d7     |
|250.61  |#869034d2     |
|251.04  |#849637ce     |
|251.48  |#849c39c9     |
|251.91  |#82a23cc5     |
|252.35  |#82a83ec0     |
|252.78  |#80ae41bc     |
|253.22  |#80b443b7     |
|253.65  |#7eba45b2     |
|253.09  |#7ec048ae     |
|254.52  |#7cc64aa9     |
|254.96  |#7ccc4da5     |
|255.39  |#7ad24fa0     |
|255.83  |#7ad85fac     |
|256.26  |#78dd6eb8     |
|256.7   |#78e37ec4     |
|257.13  |#76e98ed0     |
|257.57  |#76ee9ddb     |
|258     |#74f4ade7     |
|258.43  |#74f9bcf3     |
|258.87  |#72ffccff     |
|259.3   |#71ffffff     |
|259.74  |#71fcfcfc     |
|260.17  |#6ff6f6f6     |
|260.61  |#6ff6f6f6     |
|261.04  |#6df3f3f3     |
|261.48  |#6df3f3f3     |
|261.91  |#6bededed     |
|262.35  |#6bededed     |
|262.78  |#69e7e7e7     |
|263.22  |#69e7e7e7     |
|263.65  |#67e1e1e1     |
|264.09  |#67e1e1e1     |
|264.52  |#65dedede     |
|264.96  |#65dedede     |
|265.39  |#63d8d8d8     |
|265.83  |#63d8d8d8     |
|265.84  |#61d1d1d1     |
|266.26  |#61d1d1d1     |
|267.13  |#5fcecece     |
|267.57  |#5fcecece     |
|268     |#5dc8c8c8     |
|268.43  |#5dc8c8c8     |
|268.87  |#5bc2c2c2     |
|269.3   |#5bc2c2c2     |
|269.74  |#59bcbcbc     |
|270.17  |#59bcbcbc     |
|270.61  |#57b9b9b9     |
|271.04  |#57b9b9b9     |
|271.48  |#55b3b3b3     |
|271.91  |#55b3b3b3     |
|272.35  |#53adadad     |
|272.78  |#53adadad     |
|273.22  |#51aaaaaa     |
|273.65  |#51aaaaaa     |
|274.09  |#4fa4a4a4     |
|274.52  |#4fa4a4a4     |
|274.96  |#4d9e9e9e     |
|275.39  |#4d9e9e9e     |
|275.83  |#4b989898     |
|276.26  |#4b989898     |
|276.7   |#49959595     |
|277.13  |#49959595     |
|277.57  |#478f8f8f     |
|278     |#478f8f8f     |
|278.43  |#45898989     |
|278.87  |#45898989     |
|279.2   |#43868686     |
|279.74  |#43868686     |
|280.17  |#417f7f7f     |
|280.61  |#417f7f7f     |
|281.04  |#3f797979     |
|281.48  |#3f797979     |
|281.91  |#3d737373     |
|282.35  |#3d737373     |
|282.78  |#3b707070     |
|283.22  |#3b707070     |
|283.65  |#396a6a6a     |
|284.09  |#396a6a6a     |
|284.52  |#37646464     |
|284.96  |#37646464     |
|285.39  |#35616161     |
|285.83  |#35616161     |
|286.26  |#335b5b5b     |
|286.7   |#335b5b5b     |
|287.13  |#31555555     |
|287.57  |#31555555     |
|288     |#2f4f4f4f     |
|288.43  |#2f4f4f4f     |
|288.87  |#2d4c4c4c     |
|289.3   |#2d4c4c4c     |
|289.74  |#2b464646     |
|290.17  |#2b464646     |
|290.61  |#29404040     |
|291.04  |#29404040     |
|291.48  |#273d3d3d     |
|291.91  |#273d3d3d     |
|292.35  |#25373737     |
|292.78  |#25373737     |
|293.22  |#23313131     |
|293.65  |#23313131     |
|294.09  |#212a2a2a     |
|294.52  |#212a2a2a     |
|294.96  |#1f272727     |
|295.39  |#1f272727     |
|295.83  |#1d212121     |
|296.26  |#1d212121     |
|296.7   |#1b1b1b1b     |
|297.13  |#1b1b1b1b     |
|297.57  |#19181818     |
|298     |#19181818     |
|298.43  |#17121212     |
|298.87  |#17121212     |
|299.3   |#150c0c0c     |
|299.74  |#150c0c0c     |
|300.17  |#13060606     |
|300.61  |#13060606     |
|301.04  |#11000000     |
|301.48  |#11000000     |
|301.91  |#0f797979     |
|302.35  |#0f797979     |
|302.78  |#0d737373     |
|303.22  |#0d737373     |
|303.65  |#0b6d6d6d     |
|304.09  |#0b6d6d6d     |
|304.52  |#09676767     |
|304.92  |#09676767     |
|305.39  |#07616161     |
|305.83  |#07616161     |
|306.26  |#055b5b5b     |
|306.7   |#055b5b5b     |
|307.13  |#02555555     |
|307.57  |#02555555     |
|308     |#00525252     |
|308     |#00525252     |

## <a name="index-ids-and-index-groups-ids"></a>指数 ID 和指数组 ID

[获取每日指数 API](/rest/api/maps/weather) 使用户可以将返回的结果限制为特定的指数类型或指数组。

下表列出了可用指数 ID、其名称以及其范围集链接。 下面第二个表列出了各种指数组。

  Index Name |  ID  | 值范围
  -------------------------- |---|-----
  关节炎疼痛             |21 | [有益 - 风险极大](#beneficial-at-extreme-risk)
  哮喘                     |23|  [有益 - 风险极大](#beneficial-at-extreme-risk)
  海滩和游泳池               |10| [差 - 优 1](#poor-excellent-1)
  骑自行车                  |4| [差 - 优 1](#poor-excellent-1)
  普通感冒                |25|  [有益 - 风险极大](#beneficial-at-extreme-risk)
  堆肥                 |38| [差 - 优 1](#poor-excellent-1)
  建筑               |14| [差 - 优 1](#poor-excellent-1)
  慢性阻塞性肺病                       |44|  [有益 - 风险极大](#beneficial-at-extreme-risk)
  遛狗舒适度        |43| [差 - 优 1](#poor-excellent-1)
  驾车                    |40|  [差 - 优 2](#poor-excellent-2)
  灰尘和皮屑              |18| [低 - 极高 1](#low-extreme-1)
  现场就绪            |32| [差 - 优 1](#poor-excellent-1)
  钓鱼                    |13| [差 - 优 1](#poor-excellent-1)
  航班延误              |-3|  [极不可能 - 极有可能 2](#very-unlikely-very-likely-2)
  流感                        |26|  [有益 - 风险极大](#beneficial-at-extreme-risk)
  飞行旅行指数        |31| [优 - 差](#excellent-poor)
  燃油经济性               |37| [差 - 优 1](#poor-excellent-1)
  高尔夫天气               |5| [差 - 优 1](#poor-excellent-1)
  草地长势              |33| [差 - 优 1](#poor-excellent-1)
  头发鬈曲                 |42| [不可能 - 紧急情况](#unlikely-emergency)
  心脏健康      |16| [差 - 优 1](#poor-excellent-1)
  徒步                     |3| [差 - 优 1](#poor-excellent-1)
  家庭能源效率     |36| [差 - 优 1](#poor-excellent-1)
  搜寻                    | 20| [差 - 优 1](#poor-excellent-1)
  室内活动            | -2| [差 - 优 1](#poor-excellent-1)
  慢跑                    |2| [差 - 优 1](#poor-excellent-1)
  放风筝                |9| [差 - 优 1](#poor-excellent-1)
  草坪修剪                |28| [差 - 优 1](#poor-excellent-1)
  偏头痛          |27|  [有益 - 风险极大](#beneficial-at-extreme-risk)
  早班校车         |35| [差 - 优 1](#poor-excellent-1)
  蚊子活动          |17|[低 - 极高 1](#low-extreme-1)
  户外活动           |29| [差 - 优 1](#poor-excellent-1)
  户外烧烤           |24| [差 - 优 1](#poor-excellent-1)
  户外音乐会            |8| [差 - 优 1](#poor-excellent-1)
  正在运行                    |1|  [差 - 优 1](#poor-excellent-1)
  网球                     |6| [差 - 优 1](#poor-excellent-1)
  口渴                     |41| [低 - 极高 2](#low-extreme-2)
  航行                    |11| [差 - 优 1](#poor-excellent-1)
  购物                   |39| [差 - 优 1](#poor-excellent-1)
  窦性头痛             |30|  [有益 - 风险极大](#beneficial-at-extreme-risk)
  滑板              | 7| [差 - 优 1](#poor-excellent-1)
  滑雪天气                | 15| [差 - 优 1](#poor-excellent-1)
  雪天                  | 19| [极不可能 - 极有可能](#very-unlikely-very-likely)
  土壤湿度              | 34| [差 - 优 1](#poor-excellent-1)
  观星                 | 12| [差 - 优 1](#poor-excellent-1)

下表列出了可用指数组 (indexGroupId)：

  ID   | 组名 | 本组中的指数 |
  -------- | ------------------|------
  1       |全部 | 全部
  2       |身体疼痛 | 关节炎疼痛 (21) </br> 偏头痛 (27) </br> 窦性头痛 (30)
  3       |呼吸道 | 哮喘 (23) </br> 普通感冒 (25) </br> 流感预测 (26)
  4       |园艺 | 现场就绪 (32) </br> 草坪修剪 (28) </br> 土壤湿度 (34)</br>
  5       |环境 | 复合 (38) </br> 家庭能源效率 (36) </br> 燃油经济性 (37)
  6       |户外生活 | 户外烧烤 (24) </br> 蚊子活动 (17)
  7       |海滩和海洋 | 海滩和游泳池 (10) </br> 钓鱼 (13) </br> 航行 (11)
  8       |运动爱好者 | 钓鱼 (13) </br> 狩猎 (20) </br> 户外活动 (29)</br>
  9       |农业 |  现场就绪 (32) </br>  土壤湿度 (34)
  10      |健康产业 | 关节炎疼痛 (21) </br> 哮喘 (23) </br> 普通感冒 (25) </br> 灰尘和皮屑 (18) </br> 流感 (26) </br> 心脏健康 (16) </br> 偏头痛 (27)
  11      |户外 | 户外烧烤 (24) </br> 海滩和游泳池 (10) </br> 骑自行车 (4) </br> 户外音乐会 (8) </br>  现场就绪 (32) </br> 钓鱼 (13) </br> 高尔夫天气 (5) </br> 徒步 (3) </br> 狩猎 (20) </br> 慢跑 (2) </br> 放风筝 (9) </br> 蚊子活动 (17)</br> 草坪修剪 (28) </br> 户外活动 (29) </br> 跑步 (1) </br> 航行 (11) </br> 滑板 (7) </br> 滑雪天气 (15) </br>  土壤湿度 (34)</br> 观星 (12) </br> 网球 (6)
  12      |体育运动 | 骑自行车 (4) </br> 高尔夫天气 (5) </br> 徒步 (3) </br>  慢跑 (2) </br> 跑步 (1) </br> 滑板 (7) </br> 滑雪天气 (15) </br>网球 (6)
  13      |主页 | 家庭能源效率 (36) </br> 燃油经济性 (37) </br> 室内活动 (-2)

## <a name="daily-index-range-sets"></a>每日指数范围集

[获取每日指数 API](/rest/api/maps/weather) 将返回每个指数 ID 的范围值及其关联的类别名称。 并非所有指数的范围集都相同。 下表显示了[指数 ID 和指数组 ID](#index-ids-and-index-groups-ids) 中列出的支持的指数所使用的各种范围集。 若要了解哪些指数使用哪些范围集，请转到本文档的[指数 ID 和指数组 ID](#index-ids-and-index-groups-ids) 部分。

### <a name="poor-excellent-1"></a>差 - 优 1

  | Category Name | 开始范围 | 结束范围 |
  ----------------|--------------|------------
  差              |  0 |                2.99
  一般              |  3  |               4.99
  好              |  5  |              6.99
  非常好         |  7  |               8.99
  优秀         |  9  |               10

### <a name="poor-excellent-2"></a>差 - 优 2

 | Category Name | 开始范围 | 结束范围 |
  ----------------|--------------|------------
  差           |0              |  3
  一般           |3.01           |  6
  好           |6.01           |  7.5
  非常好      |7.51           |  8.99
  优秀      |9              |  10

### <a name="excellent-poor"></a>优 - 差

 | Category Name | 开始范围 | 结束范围 |
  ----------------|--------------|------------
  优秀      |     0.00        |    1.00
  非常好        |   1.01          |  3.00
  好             |   3.01          |  5.00
  一般             |   5.01          |  7.00
  差             |   7.01          |  10.00

### <a name="low-extreme-1"></a>低 - 极高 1

   | Category Name | 开始范围 | 结束范围 |
  ----------------|--------------|------------
  低                |  0         |        1.99
  中等           |  2         |        3.99
  高               |  4         |        5.99
  很高          |  6         |        7.99
  极高            |  8         |        10

### <a name="low-extreme-2"></a>低 - 极高 2

   | Category Name | 开始范围 | 结束范围 |
  ----------------|--------------|------------
  低                |  0            |      2.99
  中等           |  3            |      4.99
  高               |  5            |      6.99
  很高          |  7            |      8.99
  极高            |  9            |      10

### <a name="very-unlikely-very-likely"></a>极不可能 - 极有可能

 | Category Name | 开始范围 | 结束范围 |
  ----------------|--------------|------------
  极不可能      | 0     |           1.99
  不可能           | 2     |           3.99
  可能           | 4     |           5.99
  很有可能             | 6     |           7.99
  很可能        | 8     |           10

### <a name="very-unlikely-very-likely-2"></a>极不可能 - 极有可能 2

 | Category Name | 开始范围 | 结束范围 |
  ----------------|--------------|------------
  极不可能      |  0.00     |         1.00
  不可能           |  1.01     |         3.00
  可能           |  3.01     |         5.00
  很有可能             |  5.01     |         7.00
  很可能        |  7.01     |         10.00

### <a name="unlikely-emergency"></a>不可能 - 紧急情况

| Category Name | 开始范围 | 结束范围 |
  ----------------|--------------|------------
  不可能         |  0     |          2.99
  观看            |  3     |          4.99
  公告         |  5     |          6.99
  警告          |  7     |          8.99
  紧急情况        |  9     |          10

### <a name="beneficial-at-extreme-risk"></a>有益 - 风险极大

| Category Name | 开始范围 | 结束范围 |
  ----------------|--------------|------------
  有益        |    0        |        1.99
  中立           |    2        |        3.99
  有风险           |    4        |        5.99
  高风险      |    6        |        7.99
  极大风险   |    8        |        10
