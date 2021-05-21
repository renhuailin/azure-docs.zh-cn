---
title: Verizon Premium Azure CDN 规则引擎匹配条件
description: 有关 Verizon Premium Azure 内容分发网络规则引擎匹配条件的参考文档。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: efd6e6a93cd4ca79e6c4b6de69f8514e2d71b252
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "84323308"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Verizon Premium Azure CDN 规则引擎匹配条件

本文列出了 Verizon Premium Azure 内容分发网络 (CDN) [规则引擎](cdn-verizon-premium-rules-engine.md)的可用匹配条件的详细说明。

规则的第二部分是匹配条件。 可以使用匹配条件来确定特定类型的请求，以便执行一组相应的功能。

例如，可以使用匹配条件：

- 筛选对特定位置处的内容的请求。
- 筛选从特定 IP 地址或国家/地区生成的请求。
- 按标头信息筛选请求。

## <a name="match-conditions"></a><a name="top"></a>匹配条件

* [始终](#always)
* [设备](#device)
* [位置](#location)
* [源](#origin)
* [请求](#request)
* [URL](#url)

### <a name="always"></a><a name="always"></a>始终

[“始终”匹配条件](https://docs.vdms.com/cdn/Content/HRE/M/Always.htm)旨在向所有请求应用默认的一组功能。

### <a name="device"></a><a name="device"></a>设备

以下匹配条件旨在根据客户端的用户代理标识请求。

| 名称       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| 品牌名称 | 根据设备的品牌名称是否匹配以下项来标识请求： <br> **-** 特定值（[品牌名称文本](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Literal.htm)） <br> **-** 正则表达式（[品牌名称正则表达式](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Regex.htm)） <br> **-** 特定模式（[品牌名称通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Wildcard.htm)） |
| 设备 OS | 根据设备的 OS 是否匹配以下项来标识请求： <br> **-** 特定值（[设备 OS 文本](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Literal.htm)） <br> **-** 正则表达式（[设备 OS 正则表达式](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Regex.htm)） <br> **-** 特定模式（[设备 OS 通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Wildcard.htm)） |
| 设备 OS 版本 | 根据设备的 OS 版本是否匹配以下项来标识请求： <br> **-** 特定值（[设备 OS 版本文本](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Literal.htm)） <br> **-** 正则表达式（[设备 OS 版本正则表达式](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Regex.htm)） <br> **-** 特定模式（[设备 OS 版本通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Wildcard.htm)） |
| [是否为双方向？](https://docs.vdms.com/cdn/Content/HRE/M/D-Dual-Orientation.htm) | 根据设备是否支持双方向来标识请求。 |
| HTML 首选 DTD | 根据设备的 HTML 首选 DTD 是否与以下项匹配来标识请求： <br> **-** 特定值（[HTML 首选 DTD 文本](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Literal.htm)） <br> **-** 正则表达式（[HTML 首选 DTD 正则表达式](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Regex.htm)） <br> **-** 特定模式（[HTML 首选 DTD 通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Wildcard.htm)） |
| [是否为图像内联？](https://docs.vdms.com/cdn/Content/HRE/M/D-Image-Inlining.htm) | 根据设备是否支持 Base64 编码图像来标识请求。 |
| [是否为 Android？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Android.htm) | 根据设备是否使用 Android OS 来标识请求。 |
| [是否为应用？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-App.htm) | 根据本机应用程序是否请求内容来标识请求。 |
| [是否为完整桌面？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Full-Desktop.htm) | 根据设备是否提供完整桌面体验来标识请求。 |
| [是否为 iOS？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-iOS.htm) | 根据设备是否使用 iOS 来标识请求。 |
| [是否为机器人？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Robot.htm) | 根据设备是否被视为自动 HTTP 客户端（例如机器人爬网程序）来标识请求。 |
| [是否为智能电视？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smart-TV.htm) | 根据设备是否为智能电视来标识请求。 |
| [是否为智能手机？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smartphone.htm) | 根据设备是否为智能手机来标识请求。
| [是否为平板电脑？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Tablet.htm) | 根据设备是否为平板电脑来标识请求。 |
| [是否为触摸屏？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Touchscreen.htm) | 根据设备的主要指针设备是否为触摸屏来标识请求。 |
| [是否为 Windows Phone？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Windows-Phone.htm) | 根据设备是否为 Windows Mobile 6.5/Windows Phone 7 或更高版本来标识请求。 |
| [是否为无线设备？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Wireless-Device.htm) | 根据设备是否为无线设备来标识请求。 
| 市场营销名称 | 根据设备的市场营销名称是否匹配以下项来标识请求： <br> **-** 特定值（[市场营销名称文本](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Literal.htm)） <br> **-** 正则表达式（[市场营销名称正则表达式](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Regex.htm)） <br> **-** 特定模式（[市场营销名称通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Wildcard.htm)） |
| 移动浏览器 | 根据设备的浏览器是否匹配以下项来标识请求： <br> **-** 特定值（[移动浏览器文本](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Literal.htm)） <br> **-** 正则表达式（[移动浏览器正则表达式](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Regex.htm)） <br> **-** 特定模式（[移动浏览器通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Wildcard.htm)） |
| 移动浏览器版本 | 根据设备的浏览器版本是否匹配以下项来标识请求： <br> **-** 特定值（[移动浏览器版本文本](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Literal.htm)） <br> **-** 正则表达式（[移动浏览器版本正则表达式](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Regex.htm)） <br> **-** 特定模式（[移动浏览器版本通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Wildcard.htm)） |
| 模型名称 | 根据设备的模型名称是否匹配以下项来标识请求： <br> **-** 特定值（[模型名称文本](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Literal.htm)） <br> **-** 正则表达式（[模型名称正则表达式](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Regex.htm)） <br> **-** 特定模式（[模型名称通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Wildcard.htm)） |
| [是否为渐进式下载？](https://docs.vdms.com/cdn/Content/HRE/M/D-Progressive-Download.htm) | 根据设备是否支持渐进式下载来标识请求。 |
| 发布日期 | 根据设备的发布日期是否匹配以下项来标识请求： <br> **-** 特定值（[发布日期文本](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Literal.htm)） <br> **-** 正则表达式（[发布日期正则表达式](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Regex.htm)） <br> **-** 特定模式（[发布日期通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Wildcard.htm)） |
| [垂直分辨率](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Height.htm) | 根据设备的高度来标识请求。 |
| [水平分辨率](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Width.htm) | 根据设备的宽度来标识请求。 |

[返回页首](#top)

### <a name="location"></a><a name="location"></a>定位

以下匹配条件旨在根据请求者的位置标识请求。

| 名称       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [AS 编号](https://docs.vdms.com/cdn/Content/HRE/M/AS-Number.htm) | 标识源自特定网络的请求。 |
| 城市名称 | 根据请求是否源自名称与以下项匹配的城市来标识请求： <br> **-** 特定值（[城市名称文本](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Literal.htm)） <br> **-** 正则表达式（[城市名称正则表达式](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Regex.htm)） |
| [洲](https://docs.vdms.com/cdn/Content/HRE/M/Continent.htm) | 标识源自指定洲的请求。 |
| [国家/地区](https://docs.vdms.com/cdn/Content/HRE/M/Country.htm) | 标识源自指定国家/地区的请求。 |
| [DMA 代码](https://docs.vdms.com/cdn/Content/HRE/M/DMA-Code.htm) | 标识源自指定都市（指定市场区域）的请求。 |
| [纬度](https://docs.vdms.com/cdn/Content/HRE/M/Latitude.htm) | 标识源自指定纬度的请求。 |
| [经度](https://docs.vdms.com/cdn/Content/HRE/M/Longitude.htm) | 标识源自指定经度的请求。 |
| [都市代码](https://docs.vdms.com/cdn/Content/HRE/M/Metro-Code.htm) | 标识源自指定都市（指定市场区域）的请求。 |
| [邮政编码](https://docs.vdms.com/cdn/Content/HRE/M/Postal-Code.htm) | 标识源自指定邮政编码的请求。 |
| [区域代码](https://docs.vdms.com/cdn/Content/HRE/M/Region-Code.htm) | 标识源自指定区域的请求。 |

> [!NOTE]
> **我应该使用都市代码还是 DMA 代码？** <br>
这两个匹配条件都提供相同的功能。 但是，我们建议使用都市代码匹配条件根据 DMA 标识请求。

[返回页首](#top)

### <a name="origin"></a><a name="origin"></a>源

以下匹配条件旨在标识指向 CDN 存储或客户源服务器的请求。

| 名称       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [CDN 源](https://docs.vdms.com/cdn/Content/HRE/M/CDN-Origin.htm) | 标识存储在 CDN 存储上的内容的请求。 |
| [客户源](https://docs.vdms.com/cdn/Content/HRE/M/Customer-Origin.htm) | 标识存储在特定客户源服务器上的内容的请求。 |

[返回页首](#top)

### <a name="request"></a><a name="request"></a>请求

以下匹配条件旨在根据属性标识请求。

| 名称              | 目的                                                                |
|-------------------|------------------------------------------------------------------------|
| [客户端 IP 地址](https://docs.vdms.com/cdn/Content/HRE/M/Client-IP-Address.htm) | 标识源自特定 IP 地址的请求。 |
| Cookie 参数  | 根据请求是否包含与以下项匹配的 cookie 来标识请求： <br> **-** 特定值（[Cookie 参数文本](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Literal.htm)） <br> **-** 正则表达式（[Cookie 参数正则表达式](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Regex.htm)） <br> **-** 特定模式（[Cookie 参数通配符](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Wildcard.htm)） |
| [边缘 CNAME](https://docs.vdms.com/cdn/Content/HRE/M/Edge-CNAME.htm) | 标识指向特定的边缘 CNAME 的请求。 |
| 引用域 | 根据请求是否由与以下项匹配的主机名引用来标识请求： <br> **-** 特定值（[引用域文本](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Literal.htm)） <br> **-** 特定模式（[引用域通配符](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Wildcard.htm)） |
| 请求头 | 根据请求是否包含与以下项匹配的标头来标识请求： <br> **-** 特定值（[请求标头文本](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Literal.htm)） <br> **-** 正则表达式（[请求标头正则表达式](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Regex.htm)） <br> **-** 特定模式（[请求标头通配符](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Wildcard.htm)） |
| [请求方法](https://docs.vdms.com/cdn/Content/HRE/M/Request-Method.htm) | 按 HTTP 方法标识请求。 |
| [请求方案](https://docs.vdms.com/cdn/Content/HRE/M/Request-Scheme.htm) | 按 HTTP 协议标识请求。 |

[返回页首](#top)

### <a name="url"></a><a name="url"></a>URL

| 名称              | 目的                                                                |
|-------------------|------------------------------------------------------------------------|
| URL 路径 | 根据请求的相对路径（包括文件名）是否与以下项匹配来标识请求： <br> **-** 特定值（[URL 路径文本](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Literal.htm)） <br> **-** 正则表达式（[URL 路径正则表达式](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Regex.htm)） <br> **-** 特定模式（[URL 路径通配符](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Wildcard.htm)） |
| URL 路径目录 | 根据请求的相对路径是否与以下项匹配来标识请求： <br> **-** 特定值（[URL 路径目录文本](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Literal.htm)） <br> **-** 特定模式（[URL 路径目录通配符](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Wildcard.htm)） |
| URL 路径扩展名 | 根据请求的文件扩展名是否与以下项匹配来标识请求： <br> **-** 特定值（[URL 路径扩展名文本](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Literal.htm)） <br> **-** 特定模式（[URL 路径扩展名通配符](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Wildcard.htm)） |
| URL 路径文件名 | 根据请求的文件名是否与以下项匹配来标识请求： <br> **-** 特定值（[URL 路径文件名文本](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Literal.htm)） <br> **-** 特定模式（[URL 路径文件名通配符](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Wildcard.htm)） |
| URL 查询 | 根据请求的查询字符串是否与以下项匹配来标识请求： <br> **-** 特定值（[URL 查询文本](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Literal.htm)） <br> **-** 正则表达式（[URL 查询正则表达式](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Regex.htm)） <br> **-** 特定模式（[URL 查询通配符](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Wildcard.htm)） |
| URL 查询参数 | 根据请求是否包含已设置为与以下项匹配的值的查询字符串参数来标识请求： <br> **-** 特定值（[URL 查询参数文本](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Literal.htm)） <br> **-** 特定模式（[URL 查询参数通配符](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Wildcard.htm)） |

[返回页首](#top)

有关最新匹配条件，请参阅 [Verizon 规则引擎文档](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio)。

## <a name="next-steps"></a>后续步骤

- [Azure 内容分发网络概述](cdn-overview.md)
- [规则引擎参考](cdn-verizon-premium-rules-engine-reference.md)
- [规则引擎条件表达式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [规则引擎功能](cdn-verizon-premium-rules-engine-reference-features.md)
- [使用规则引擎重写默认 HTTP 行为](cdn-verizon-premium-rules-engine.md)
