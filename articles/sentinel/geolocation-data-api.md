---
title: 通过 REST API 用地理位置数据扩充 Azure Sentinel 中的实体 | Microsoft Docs
description: 本文介绍如何通过 REST API 用地理位置数据扩充 Azure Sentinel 中的实体。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2021
ms.author: bagol
ms.openlocfilehash: 483a228dc172acc3ce0cb44e5f9ac0eba1da807c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481271"
---
# <a name="enrich-entities-in-azure-sentinel-with-geolocation-data-via-rest-api-public-preview"></a>通过 REST API 用地理位置数据扩充 Azure Sentinel 中的实体（公共预览版）

本文介绍如何通过 REST API 用地理位置数据扩充 Azure Sentinel 中的实体。

> [!IMPORTANT]
> 此功能目前处于预览状态。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

## <a name="common-uri-parameters"></a>常见 URI 参数

下面是地理位置 API 的常见 URI 参数：




| 名称 | 在 | 必需 | 类型 | 说明 |
|-|-|-|-|-|
| “{subscriptionId}” | path | 是 | GUID | Azure 订阅 ID |
| “{resourceGroupName}” | path | 是 | 字符串 | 订阅中的资源组的名称 |
| “{api-version}” | query | 是 | 字符串 | 用于发出此请求的协议的版本。 从 2021 年 4 月 30 日起，地理位置 API 版本为 2019-01-01-preview。|
| {ipAddress} | query | 是 | 字符串 | 需要地理位置信息的 IP 地址，采用 IPv4 或 IPv6 格式。   |
|

## <a name="enrich-ip-address-with-geolocation-information"></a>使用地理位置信息扩充 IP 地址

此命令检索给定 IP 地址的地理位置数据。

### <a name="request-uri"></a>请求 URI

| 方法 | 请求 URI |
|-|-|
| **GET** | `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.SecurityInsights/enrichment/ip/geodata/?ipaddress={ipAddress}&api-version={api-version}` |
|

### <a name="responses"></a>响应

|状态代码  |说明  |
|---------|---------|
|**200**     |   Success      |
|**400**     |      未提供 IP 地址或格式无效    |
|**404**     | 找不到此 IP 地址的地理位置数据         |
|**429**     |      请求过多，请在指定的时间范围内重试    |
|     |         |

### <a name="fields-returned-in-the-response"></a>响应中返回的字段

|字段名称  |说明  |
|---------|---------|
|**ASN**     |  与此 IP 地址关联的自治系统编号       |
|运营商     |  此 IP 地址的运营商名称       |
|城市     |   此 IP 地址所在的城市      |
|cityCf     | 表示“城市”字段中的值正确时的置信度数值评级，范围为 0 - 100        |
|洲     | 此 IP 地址所在的洲        |
|**country**     |此 IP 地址所在的国家/地区        |
|countryCf     |   表示“国家/地区”字段中的值正确时的置信度数值评级，范围为 0 - 100      |
|ipAddr     |   IP 地址的点分十进制或冒号分隔的字符串表示形式      |
|ipRoutingType     |   此 IP 地址的连接类型的说明      |
|**latitude**     |     此 IP 地址的纬度    |
|**longitude**     |  此 IP 地址的经度       |
|组织     |  此 IP 地址的组织名称       |
|organizationType     | 此 IP 地址的组织类型        |
|**region**     |    此 IP 地址所在的地理区域     |
|**state**     |  此 IP 地址所在的州       |
|stateCf     | 表示“州”字段中的值正确时的置信度数值评级，范围为 0 - 100        |
|stateCode     |   此 IP 地址所在州的缩写名称      |
|     |         |


## <a name="throttling-limits-for-the-api"></a>针对 API 限制的限制

此 API 的限制为每个用户每小时 100 次调用。

### <a name="sample-response"></a>示例响应

```rest
"body":
{
    "asn": "12345",
    "carrier": "Microsoft",
    "city": "Redmond",
    "cityCf": 90,
    "continent": "north america",
    "country": "united states",
    "countryCf": 99
    "ipAddr": "1.2.3.4",
    "ipRoutingType": "fixed",
    "latitude": "40.2436",
    "longitude": "-100.8891",
    "organization": "Microsoft",
    "organizationType": "tech",
    "region": "western usa",
    "state": "washington",
    "stateCf": null
    "stateCode": "wa"
}
```

## <a name="next-steps"></a>后续步骤

要详细了解 Azure Sentinel，请参阅以下文章：

- 详细了解实体：

    - [Azure Sentinel 实体类型引用](entities-reference.md)
    - [在 Azure Sentinel 中使用实体对数据进行分类和分析](entities-in-azure-sentinel.md)
    - [将数据字段映射到 Azure Sentinel 中的实体](map-data-fields-to-entities.md)

- 了解 [Azure Sentinel API](/rest/api/securityinsights/) 的其他用法
