---
title: 定位点定位策略
description: 了解调用定位 API 时的不同策略
author: pamistel
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: pamistel
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 13aa12be5a336363bbe3bcbf3e3fb354a8fa3074
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048470"
---
# <a name="understanding-the-anchorlocatecriteria-class"></a>了解 AnchorLocateCriteria 类
在本文中，你将了解查询定位点时可使用的不同选项。 我们将仔细查看 AnchorLocateCriteria 类、其选项和有效的选项组合。

## <a name="anchor-locate-criteria"></a>定位点定位条件
[AnchorLocateCriteria 类](/dotnet/api/microsoft.azure.spatialanchors.anchorlocatecriteria)可帮助你在服务中查询以前创建的定位点。 每个观察程序随时可以使用一个 AnchorLocateCriteria 对象。 每个 AnchorLocateCriteria 对象必须仅包括以下属性之一：[Identifiers](#identifiers)、[NearAnchor](#nearanchor) 或 [NearDevice](#neardevice)。 如果需要，可以设置其他属性（例如 [Strategy](#strategy)、[BypassCache](#bypasscache) 和 [RequestedCategories](#requestedcategories)）。 

### <a name="properties"></a>属性
仅在观察程序中定义以下属性之一：
#### <a name="identifiers"></a>标识符
默认值：空字符串数组

使用 Identifiers，可以定义要定位的定位点的定位点 ID 列表。 在成功创建定位点后，最初会返回定位点 ID。 在指定 Identifiers 的情况下，AnchorLocateCriteria 会将请求的定位点集合限制为具有匹配定位点 ID 的定位点。 使用字符串数组指定此属性。 

#### <a name="nearanchor"></a>NearAnchor
默认值：未设置

使用 NearAnchor，你可以指定 AnchorLocateCriteria 限制请求的定位点集合为需要与所选定位点保持的距离内的定位点。 必须提供此所选定位点作为源定位点。 还可以设置需要与源定位点保持的距离和返回的最大定位点数量，以进一步限制搜索。
使用 NearAnchorCriteria 对象指定此属性。

#### <a name="neardevice"></a>NearDevice
默认值：未设置

使用 NearDevice，可以指定 AnchorLocateCriteria 限制请求的定位点集合为靠近设备物理位置的定位点。 任何启用的传感器都将用于帮助发现设备周围的定位点。 为了提升查找定位点的几率，你应该配置 SensorCapabilities，以便会话可以访问所有合适的传感器。 有关设置和使用此属性的详细信息，请参阅[粗糙重新局部化 - Azure 空间定位点 | Microsoft Docs](./coarse-reloc.md) 和如何在 [C#](../how-tos/set-up-coarse-reloc-unity.md)、[Objective-C](../how-tos/set-up-coarse-reloc-unity.md)、[Swift](../how-tos/set-up-coarse-reloc-swift.md)、[Java](../how-tos/set-up-coarse-reloc-java.md)、[C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md) 和 [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md) 中使用粗糙重新局部化创建和定位定位点。
使用 NearDeviceCriteria 对象指定此属性。

### <a name="additional-properties"></a>其他属性
#### <a name="bypasscache"></a>BypassCache
默认值：false

在会话中创建或找到定位点后，它也会存储在缓存中。  如果将此属性设置为 false，则同一会话中的任何后续查询都将返回缓存的值。 不会向 ASA 服务发出请求。

#### <a name="requestedcategories"></a>RequestedCategories
默认值：属性 |空间

此属性用于确定使用 AnchorLocateCriteria 从查询返回的数据。 默认值返回属性和空间数据，如果同时需要属性和空间数据，则不应更改此值。 可以使用 AnchorDataCategory 枚举指定此属性。

AnchorDataCategory 枚举值 | 返回的数据
-----|------------
None | 不返回任何数据
属性| 返回包含 AppProperties 的定位点属性。
空间| 返回有关定位点的空间信息。

#### <a name="strategy"></a>策略
默认值：AnyStrategy

策略进一步定义应如何定位定位点。 可以使用 LocateStrategy 枚举指定 Strategy 属性。

LocateStrategy 枚举值 | 说明
---------------|------------
AnyStrategy | 此策略允许系统使用 VisualInformation 和关系策略的组合来查找定位点。 
VisualInformation|此策略通过将当前周围环境中的可视化信息与定位点的视觉对象的内存占用情况匹配，尝试查找定位点。 定位点的视觉对象内存占用情况是指当前与定位点相关的视觉信息。 在创建定位点时，通常不会以独占方式收集此视觉信息。 目前，只允许将此策略与 NearDevice 或 Identifiers 属性结合使用。
关系|此策略将尝试使用现有的[连接的定位点](./anchor-relationships-way-finding.md#connect-anchors)查找定位点。 目前，只允许将此策略与 NearAnchor 或 Identifiers 属性结合使用。 与 Identifiers 属性一起使用时，要求同一会话中的用户之前应已定位好定位点，且与 Identifiers 数组中指定了其 ID 的定位点建立了连接关系。 


### <a name="valid-combinations-of-locatestrategy-and-anchorlocatecriteria-properties"></a>LocateStrategy 和 AnchorLocateCriteria 属性的有效组合 

并非所有的 Strategy 和 AnchorLocateCriteria 属性组合都获得了系统允许。 下表显示了允许的组合：



属性 | AnyStrategy | 关系 | VisualInformation
-------- | ------------|--------------|-------------------
标识符 | &check;    | &check;     | &check;
NearAnchor  | &check;   （将默认为 Relationship） | &check;    | 
NearDevice  | &check;    |   | &check;




## <a name="next-steps"></a>后续步骤

有关使用 AnchorLocateCriteria 类的更多示例，请参阅[如何使用 Azure 空间定位点创建和定位定位点](../create-locate-anchors-overview.md)。