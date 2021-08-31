---
title: Microsoft Azure Maps Creator 中的 Facility Ontology
description: 用于描述 Azure Maps Creator 的特征类定义的 Facility Ontology
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/14/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
zone_pivot_groups: facility-ontology-schema
ms.openlocfilehash: c08faefc795fd65c71163bd9f8c24a1f8cd939f8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725614"
---
# <a name="facility-ontology"></a>Facility Ontology

Facility Ontology 定义了 Azure Maps Creator 内部在 Creator 数据集中存储设施数据的方式。  除了定义内部设施数据结构外，Facility Ontology 也通过 WFS API 对外公开。 当 WFS API 用于查询数据集中的设施数据时，响应格式是由提供给该数据集的本体定义的。

概括地讲，Facility Ontology 将数据集划分为若干个特征类。 所有特征类都共享一组通用的属性，例如 `ID` 和 `Geometry`。  除通用属性集以外，每个特征类还定义一组属性。 每个属性都由其数据类型和约束来定义。 部分特征类具有一些依赖于其他特征类的属性。 依赖属性等于另一个特征类的 `ID`。  

## <a name="changes-and-revisions"></a>更改和修订

:::zone pivot="facility-ontology-v1"

Facility 1.0 包含对 [Azure Maps 服务](https://aka.ms/AzureMaps)的 Facility 特征类定义的修订。

:::zone-end

:::zone pivot="facility-ontology-v2"

Facility 2.0 包含对 [Azure Maps 服务](https://aka.ms/AzureMaps)的 Facility 特征类定义的修订。

:::zone-end

### <a name="major-changes"></a>主要更改

:::zone pivot="facility-ontology-v1"

修复了以下约束验证检查：

* 对 `isObstruction = true` 的独占性或 `lineElement` 和 `areaElement` 特征类是否存在 `obstructionArea` 的约束验证检查。

* 对 `isRoutable = true` 的独占性或 `category` 特征类是否存在 `routeThroughBehavior` 的约束验证检查。
:::zone-end

:::zone pivot="facility-ontology-v2"

* 添加了用于保留墙、柱等的结构特征类。
* 清理了旨在丰富路线方案的属性。 当前路线引擎不支持这些属性。

:::zone-end

## <a name="unit"></a>unit

`unit` 特征类定义了导航代理可以占用和遍历的物理和非重叠区域。 `unit` 可以是走廊、房间、庭院等区域。

几何类型：Polygon

:::zone pivot="facility-ontology-v1"

| 属性           | 类型                        | 必需 | 说明                                                  |
|--------------------|-----------------------------|----------|--------------------------------------------------------------|
|`originalId`        | 字符串     |true      | 从客户端数据派生的 ID。 允许的最大长度为 1000。|
|`externalId`        | 字符串     |true      | 客户端用于将特征与不同数据集（如内部数据库）中的另一个特征进行关联的 ID。 允许的最大长度为 1000。|
|`categoryId`        | [category.Id](#category)     |是      | [`category`](#category) 特征的 ID。|
|`isOpenArea`        | boolean（默认值为 `null`。）                    |false     | 表示单元是否为开放区域。 如果设置为 `true`，则[结构](#structure)不环绕单元边界，且导航代理无需 [`opening`](#opening) 即可进入 `unit`。 默认情况下，单元由物理屏障环绕，并且仅当在单元边界上放置 opening 特征时才为开放区域。 如果开放区域单元中需要墙体，它们可以表示为 [`lineElement`](#lineelement) 或 [`areaElement`](#areaelement)，其中 `isObstruction` 属性等于 `true`。|
|`navigableBy`        | enum ["pedestrian", "wheelchair", "machine", "bicycle", "automobile", "hiredAuto", "bus", "railcar", "emergency", "ferry", "boat"]  | false      |指明可遍历单元的导航代理的类型。 如果未指定，则假定单元由任何导航代理遍历。 |
|`isRoutable`        | boolean（默认值为 `null`。）                      | false    |  确定单元是否是路线图的一部分。 如果设置为 `true`，则单元可以在路线体验中用作源/目标或中间节点。 |
|`routeThroughBehavior`        | enum ["disallowed", "allowed", "preferred"] |  false     | 确定是否允许在单元中进行导航。 如果未指定，它将从 `categoryId` 属性中引用的类别特征继承其值。 如果指定，它将替代其类别特征中给定的值。 |
|`nonPublic`        |  boolean| false       | 如果为 `true`，则只有特权用户才能导航该单元。  默认值是 `false`。 |
| `levelId`          | [level.Id](#level)        | 是     | 楼层特征的 ID。 |
|`occupants`         |  [directoryInfo.Id](#directoryinfo) 的数组 |    false |    [directoryInfo](#directoryinfo) 特征的 ID。 用于表示特征中的一个或多个使用者。 |
|`addressId`         | [directoryInfo.Id](#directoryinfo) | 是     | [directoryInfo](#directoryinfo) 特征的 ID。 用于表示特征的地址。|
|`addressRoomNumber`         |  [directoryInfo.Id](#directoryinfo) | 是     | 单元的房间/单间/公寓/套房编号。|
|`name` |    字符串 |    false |    以本地语言表示的特征的名称。 允许的最大长度为 1000。 |
|`nameSubtitle` |    字符串 |    false |   显示在特征的 `name` 下的副标题。 可用于以不同的语言显示名称等等。  允许的最大长度为 1000。|
|`nameAlt` |    字符串 |    false |   用于该特征的替代名称。 允许的最大长度为 1000。 |
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | 将特征表示为点的 [GeoJSON Point 几何图形](/rest/api/maps/wfs/get-feature-preview#featuregeojson)。 可用于定位特征的标签。|

:::zone-end

:::zone pivot="facility-ontology-v2"

| 属性           | 类型                        | 必需 | 说明                                                  |
|--------------------|-----------------------------|----------|--------------------------------------------------------------|
|`originalId`        | 字符串     |true      | 从客户端数据派生的 ID。 允许的最大长度为 1000。|
|`externalId`        | 字符串     |true      | 客户端用于将特征与不同数据集（如内部数据库）中的另一个特征进行关联的 ID。 允许的最大长度为 1000。|
|`categoryId`        | [category.Id](#category)     |是      | [`category`](#category) 特征的 ID。|
|`isOpenArea`        | boolean（默认值为 `null`。）                    |false     | 表示单元是否为开放区域。 如果设置为 `true`，则[结构](#structure)不环绕单元边界，且导航代理无需 [`opening`](#opening) 即可进入 `unit`。 默认情况下，单元由物理屏障环绕，并且仅当在单元边界上放置 opening 特征时才为开放区域。 如果开放区域单元中需要墙体，它们可以表示为 [`lineElement`](#lineelement) 或 [`areaElement`](#areaelement)，其中 `isObstruction` 属性等于 `true`。|
|`isRoutable`        | boolean（默认值为 `null`。）                     | false    |  确定单元是否是路线图的一部分。 如果设置为 `true`，则单元可以在路线体验中用作源/目标或中间节点。 |
| `levelId`          | [level.Id](#level)        | 是     | 楼层特征的 ID。 |
|`occupants`         |  [directoryInfo.Id](#directoryinfo) 的数组 |    false |    [directoryInfo](#directoryinfo) 特征的 ID。 用于表示特征中的一个或多个使用者。 |
|`addressId`         | [directoryInfo.Id](#directoryinfo) | 是     | [directoryInfo](#directoryinfo) 特征的 ID。 用于表示特征的地址。|
|`addressRoomNumber`         |  [directoryInfo.Id](#directoryinfo) | 是     | 单元的房间/单间/公寓/套房编号。|
|`name` |    字符串 |    false |    以本地语言表示的特征的名称。  允许的最大长度为 1000。|
|`nameSubtitle` |    字符串 |    false |   显示在特征的 `name` 下的副标题。 可用于以不同的语言显示名称等等。  允许的最大长度为 1000。|
|`nameAlt` |    字符串 |    false |   用于该特征的替代名称。  允许的最大长度为 1000。|
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | 将特征表示为点的 [GeoJSON Point 几何图形](/rest/api/maps/wfs/get-feature-preview#featuregeojson)。 可用于定位特征的标签。|

:::zone-end

:::zone pivot="facility-ontology-v2"

## <a name="structure"></a>structure

`structure` 特征类定义无法导航的物理和非重叠区域。 可以是墙、柱等。

几何类型：Polygon

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`originalId`        | 字符串     |true      | 从客户端数据派生的 ID。 允许的最大长度为 1000。|
|`externalId`        | 字符串     |true      | 客户端用于将特征与不同数据集（如内部数据库）中的另一个特征进行关联的 ID。 允许的最大长度为 1000。|
|`categoryId`        | [category.Id](#category)      |是      | [`category`](#category) 特征的 ID。|
| `levelId`          |  [level.Id](#level)            | 是     | [`level`](#level) 特征的 ID。 |
|`name` |    字符串 |    false |    以本地语言表示的特征的名称。 允许的最大长度为 1000。 |
|`nameSubtitle` |    字符串 |    false |   显示在特征的 `name` 下的副标题。 可用于以不同的语言显示名称等等。 允许的最大长度为 1000。 |
|`nameAlt` |    字符串 |    false |   用于该特征的替代名称。  允许的最大长度为 1000。|
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | 将特征表示为点的 [GeoJSON Point 几何图形](/rest/api/maps/wfs/get-feature-preview#featuregeojson)。 可用于定位特征的标签。|

:::zone-end

## <a name="zone"></a>区域

`zone` 特征类定义一个虚拟区域，比如 WiFi 区域或紧急集合区域。 区域可以用作目的地，但不是为了直达交通。

几何类型：Polygon

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`originalId`        | 字符串     |true      | 从客户端数据派生的 ID。 允许的最大长度为 1000。|
|`externalId`        | 字符串     |true      | 客户端用于将特征与不同数据集（如内部数据库）中的另一个特征进行关联的 ID。 允许的最大长度为 1000。|
|`categoryId`        | [category.Id](#category)      |是      | [`category`](#category) 特征的 ID。|
| `setId`          | 字符串         | true     |对于表示多楼层区域的区域特征是必需的。 `setId` 是跨多个楼层的区域的唯一 ID。 `setId` 使一个在不同楼面有不同覆盖率的区域能够在不同楼层上用不同的几何图形来表示。 `setId` 可以是任何字符串，并且区分大小写。 建议 `setId` 为 GUID。  允许的最大长度为 1000。|
| `levelId`          |  [level.Id](#level)             | 是     | [`level`](#level) 特征的 ID。 |
|`name` |    字符串 |    false |    以本地语言表示的特征的名称。  允许的最大长度为 1000。|
|`nameSubtitle` |    字符串 |    false |   显示在特征的 `name` 下的副标题。 可用于以不同的语言显示名称等等。  允许的最大长度为 1000。|
|`nameAlt` |    字符串 |    false |   用于该特征的替代名称。 允许的最大长度为 1000。 |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | 将特征表示为点的 [GeoJSON Point 几何图形](/rest/api/maps/wfs/get-feature-preview#featuregeojson)。 可用于定位特征的标签。|

## <a name="level"></a>level

`level` 类特征定义了建筑物在设定标高上的一个区域。 例如，建筑物的楼层，其中包含一组特征，比如 [`units`](#unit)。  

几何类型：MultiPolygon

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`originalId`        | 字符串     |true      | 从客户端数据派生的 ID。 允许的最大长度为 1000。|
|`externalId`        | 字符串     |true      | 客户端用于将特征与不同数据集（如内部数据库）中的另一个特征进行关联的 ID。 允许的最大长度为 1000。|
|`categoryId`        | [category.Id](#category)    |是      | [`category`](#category) 特征的 ID。|
| `ordinal`          | integer        | true     | 级别编号。 由 [`verticalPenetration`](#verticalpenetration) 特征用于确定楼层的相对顺序，以帮助确定行进方向。 一般做法是，底层从 0 开始。 向上每层加 1，向下每层减 1。 可以用任何数字对其建模，只要较高的物理楼层由较高的序数值表示即可。 |
| `abbreviatedName`          | 字符串        | false     | 一个由四个字符组成的缩写的楼层名称，如电梯按钮上的标识。  允许的最大长度为 1000。|
| `heightAboveFacilityAnchor`          | Double         | false     | [`facility.anchorHeightAboveSeaLevel`](#facility) 以上楼层楼面的垂直距离，以米为单位。 |
| `verticalExtent`          | Double         | false     | 楼层的垂直范围，以米为单位。 如未提供，则默认为 [`facility.defaultLevelVerticalExtent`](#facility)。|
|`name` |    字符串 |    false |    以本地语言表示的特征的名称。  允许的最大长度为 1000。|
|`nameSubtitle` |    字符串 |    false |   显示在特征的 `name` 下的副标题。 可用于以不同的语言显示名称等等。  允许的最大长度为 1000。|
|`nameAlt` |    字符串 |    false |   用于该特征的替代名称。  允许的最大长度为 1000。|
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | 将特征表示为点的 [GeoJSON Point 几何图形](/rest/api/maps/wfs/get-feature-preview#featuregeojson)。 可用于定位特征的标签。|

## <a name="facility"></a>设备

`facility` 特征类定义场地面积、建筑占地面积等等。

几何类型：MultiPolygon

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`originalId`        | 字符串     |true      | 从客户端数据派生的 ID。 允许的最大长度为 1000。|
|`externalId`        | 字符串     |true      | 客户端用于将特征与不同数据集（如内部数据库）中的另一个特征进行关联的 ID。 允许的最大长度为 1000。|
|`categoryId`        | [category.Id](#category)      |是      | [`category`](#category) 特征的 ID。|
|`occupants`         | [directoryInfo.Id](#directoryinfo) 的数组 |    false |    [directoryInfo](#directoryinfo) 特征的 ID。 用于表示特征中的一个或多个使用者。 |
|`addressId`         | [directoryInfo.Id](#directoryinfo)  | 是     | [directoryInfo](#directoryinfo) 特征的 ID。 用于表示特征的地址。|
|`name` |    字符串 |    false |    以本地语言表示的特征的名称。 允许的最大长度为 1000。 |
|`nameSubtitle` |    字符串 |    false |   显示在特征的 `name` 下的副标题。 可用于以不同的语言显示名称等等。 允许的最大长度为 1000。 |
|`nameAlt` |    字符串 |    false |   用于该特征的替代名称。  允许的最大长度为 1000。|
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | 将特征表示为点的 [GeoJSON Point 几何图形](/rest/api/maps/wfs/get-feature-preview#featuregeojson)。 可用于定位特征的标签。|
|`anchorHeightAboveSeaLevel` |  Double | false | 定位点高于海平面的高度，以米为单位。 海平面由 EGM 2008 定义。|
|`defaultLevelVerticalExtent` |  Double| false | 楼层垂直范围的默认值，以米为单位。|

## <a name="verticalpenetration"></a>verticalPenetration

`verticalPenetration` 类特征定义了一个区域，在集合中使用时，该区域表示在楼层之间垂直导航的方法。 它可用于为楼梯、电梯等建模。 几何图形可以与单元和其他垂直穿透特征重叠。

几何类型：Polygon

:::zone pivot="facility-ontology-v1"

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`originalId`        | 字符串     |true      | 从客户端数据派生的 ID。 允许的最大长度为 1000。|
|`externalId`        | 字符串     |true      | 客户端用于将特征与不同数据集（如内部数据库）中的另一个特征进行关联的 ID。 允许的最大长度为 1000。|
|`categoryId`        | [category.Id](#category)      |是      | [`category`](#category) 特征的 ID。|
| `setId`          | 字符串       | true     | 垂直穿透特征必须成套使用，以连接多个楼层。 同一集合中的垂直穿透特征被视为是相同的。 `setId` 可以是任何字符串，并且区分大小写。 建议使用 GUID 作为 `setId`。  允许的最大长度为 1000。|
| `levelId`          | [level.Id](#level)         | 是     | 楼层特征的 ID。 |
|`direction`         |  string enum [ "both", "lowToHigh", "highToLow", "closed" ]| false     | 此特征允许的行进方向。 [`level`](#level) 特征上的 ordinal 属性用于确定高低顺序。|
|`navigableBy`        | enum ["pedestrian", "wheelchair", "machine", "bicycle", "automobile", "hiredAuto", "bus", "railcar", "emergency", "ferry", "boat"]  | false      |指明可遍历单元的导航代理的类型。 如果未指定，则单元由任何导航代理遍历。 |
|`nonPublic`        |  boolean| false       | 如果为 `true`，则只有特权用户才能导航该单元。  默认值是 `false`。 |
|`name` |    字符串 |    false |    以本地语言表示的特征的名称。  允许的最大长度为 1000。|
|`nameSubtitle` |    字符串 |    false |   显示在特征的 `name` 下的副标题。 可用于以不同的语言显示名称等等。  允许的最大长度为 1000。|
|`nameAlt` |    字符串 |    false |   用于该特征的替代名称。 允许的最大长度为 1000。 |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | 将特征表示为点的 [GeoJSON Point 几何图形](/rest/api/maps/wfs/get-feature-preview#featuregeojson)。 可用于定位特征的标签。|

:::zone-end

:::zone pivot="facility-ontology-v2"

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`originalId`        | 字符串     |true      | 从客户端数据派生的 ID。 允许的最大长度为 1000。|
|`externalId`        | 字符串     |true      | 客户端用于将特征与不同数据集（如内部数据库）中的另一个特征进行关联的 ID。 允许的最大长度为 1000。|
|`categoryId`        | [category.Id](#category)      |是      | [`category`](#category) 特征的 ID。|
| `setId`          | 字符串       | true     | 垂直穿透特征必须成套使用，以连接多个楼层。 同一集合中的垂直穿透特征是相连的。 `setId` 可以是任何字符串，并且区分大小写。 建议使用 GUID 作为 `setId`。 允许的最大长度为 1000。 |
| `levelId`          | [level.Id](#level)         | 是     | 楼层特征的 ID。 |
|`direction`         |  string enum [ "both", "lowToHigh", "highToLow", "closed" ]| false     | 此特征允许的行进方向。 [`level`](#level) 特征上的 ordinal 属性用于确定高低顺序。|
|`name` |    字符串 |    false |    以本地语言表示的特征的名称。  允许的最大长度为 1000。|
|`nameSubtitle` |    字符串 |    false |   显示在特征的 `name` 下的副标题。 可用于以不同的语言显示名称等等。  允许的最大长度为 1000。|
|`nameAlt` |    字符串 |    false |   用于该特征的替代名称。 允许的最大长度为 1000。 |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | 将特征表示为点的 [GeoJSON Point 几何图形](/rest/api/maps/wfs/get-feature-preview#featuregeojson)。 可用于定位特征的标签。|

:::zone-end

## <a name="opening"></a>营业

`opening` 类特征定义两个单元之间或 `unit` 和 `verticalPenetration` 之间的可遍历边界。

几何类型：LineString

:::zone pivot="facility-ontology-v1"

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`originalId`        | 字符串     |true      | 从客户端数据派生的 ID。 允许的最大长度为 1000。|
|`externalId`        | 字符串     |true      | 客户端用于将特征与不同数据集（如内部数据库）中的另一个特征进行关联的 ID。 允许的最大长度为 1000。|
|`categoryId`        |[category.Id](#category)     |是      | 类别特性的 ID。|
| `levelId`          | [level.Id](#level)        | 是     | 楼层特征的 ID。 |
| `isConnectedToVerticalPenetration` | boolean | false | 此特征是否连接到其一侧的 `verticalPenetration` 特征。 默认值是 `false`。 |
|`navigableBy`        | enum ["pedestrian", "wheelchair", "machine", "bicycle", "automobile", "hiredAuto", "bus", "railcar", "emergency", "ferry", "boat"]  | false      |指明可遍历单元的导航代理的类型。 如果未指定，则单元由任何导航代理遍历。 |
| `accessRightToLeft`| enum [ "prohibited", "digitalKey", "physicalKey", "keyPad", "guard", "ticket", "fingerprint", "retina", "voice", "face", "palm", "iris", "signature", "handGeometry", "time", "ticketChecker", "other"] | false | 从右到左穿过开口时的进入方法。 左侧和右侧由特征几何图形中的顶点来确定（位于第一个顶点并面向第二个顶点）。 省略此属性意味着没有进入限制。|
| `accessLeftToRight`| enum [ "prohibited", "digitalKey", "physicalKey", "keyPad", "guard", "ticket", "fingerprint", "retina", "voice", "face", "palm", "iris", "signature", "handGeometry", "time", "ticketChecker", "other"] | false | 从左到右穿过开口时的进入方法。 左侧和右侧由特征几何图形中的顶点来确定（位于第一个顶点并面向第二个顶点）。 省略此属性意味着没有进入限制。|
| `isEmergency` | boolean | false | 如果为 `true`，则只有在紧急情况下才可以导航开口。 默认值为 `false` |
|`anchorPoint` | [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | 将特征表示为点的 [GeoJSON Point 几何图形](/rest/api/maps/wfs/get-feature-preview#featuregeojson)。 可用于定位特征的标签。|

:::zone-end

:::zone pivot="facility-ontology-v2"

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`originalId`        | 字符串     |true      | 从客户端数据派生的 ID。 允许的最大长度为 1000。|
|`externalId`        | 字符串     |true      | 客户端用于将特征与不同数据集（如内部数据库）中的另一个特征进行关联的 ID。 允许的最大长度为 1000。|
|`categoryId`        |[category.Id](#category)     |是      | 类别特性的 ID。|
| `levelId`          | [level.Id](#level)        | 是     | 楼层特征的 ID。 |
|`anchorPoint` | [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | 将特征表示为点的 [GeoJSON Point 几何图形](/rest/api/maps/wfs/get-feature-preview#featuregeojson)。 可用于定位特征的标签。|

:::zone-end

## <a name="directoryinfo"></a>directoryInfo

`directoryInfo` 对象类特征定义一个单元、设施或单元/设施使用者的名称、地址、电话号码、网站和工作时间。

几何类型：None

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`originalId`        | 字符串     |true      | 从客户端数据派生的 ID。 允许的最大长度为 1000。|
|`externalId`        | 字符串     |true      | 客户端用于将特征与不同数据集（如内部数据库）中的另一个特征进行关联的 ID。 允许的最大长度为 1000。|
|`streetAddress`        |字符串    |false    |地址中的街道地址部分。  允许的最大长度为 1000。 |
|`unit`        |字符串    |false    |地址中的单元编号部分。  允许的最大长度为 1000。 |
|`locality`|    字符串|    false    |地址的位置。 例如：市、自治区、村。 允许的最大长度为 1000。|
|`adminDivisions`|    字符串|    false    |地址的行政区划部分，从最小到最大（县、省/市/自治区、国家/地区）。 例如：["King", "Washington", "USA" ] or ["West Godavari", "Andhra Pradesh", "IND" ]。 允许的最大长度为 1000。|
|`postalCode`|    字符串 |    false    |地址中的邮政编码部分。 允许的最大长度为 1000。|
|`name` |    字符串 |    false |    以本地语言表示的特征的名称。  允许的最大长度为 1000。|
|`nameSubtitle` |    字符串 |    false |   显示在特征的 `name` 下的副标题。 可用于以不同的语言显示名称等等。 允许的最大长度为 1000。 |
|`nameAlt` |    字符串 |    false |   用于该特征的替代名称。 允许的最大长度为 1000。 |
|`phoneNumber` |    字符串 |    false |    电话号码。 允许的最大长度为 1000。 |
|`website` |    字符串 |    false |  网站 URL。 允许的最大长度为 1000。 |
|`hoursOfOperation` |    字符串 |    false |   以文本形式显示的运行小时数，遵循[开放街道地图规范](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification)。 允许的最大长度为 1000。 |

## <a name="pointelement"></a>pointElement

`pointElement` 是一个类特征，定义了一个单元中的点特征，例如急救箱或洒水喷头。

几何类型：MultiPoint

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`originalId`        | 字符串     |true      | 从客户端数据派生的 ID。 允许的最大长度为 1000。|
|`externalId`        | 字符串     |true      | 客户端用于将特征与不同数据集（如内部数据库）中的另一个特征进行关联的 ID。 允许的最大长度为 1000。|
|`categoryId`        |[category.Id](#category)      |是      | [`category`](#category) 特征的 ID。|
| `unitId`          | 字符串     | true     | 包含此特征的 [`unit`](#unit) 特征的 ID。  允许的最大长度为 1000。|
| `isObstruction`          | boolean（默认值为 `null`。） | false     | 如果为 `true`，则此特征表示在通过包含的单元特征时要避开的障碍。 |
|`name` |    字符串 |    false |    以本地语言表示的特征的名称。  允许的最大长度为 1000。|
|`nameSubtitle` |    字符串 |    false |   显示在特征的 `name` 下的副标题。 可用于以不同的语言显示名称等等。 允许的最大长度为 1000。 |
|`nameAlt` |    字符串 |    false |   用于该特征的替代名称。  允许的最大长度为 1000。|

## <a name="lineelement"></a>lineElement

`lineElement` 是一个类特征，定义了单元中的线特征，如隔墙或窗户。

几何类型：LinearMultiString

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`originalId`        | 字符串     |true      | 从客户端数据派生的 ID。 允许的最大长度为 1000。|
|`externalId`        | 字符串     |true      | 客户端用于将特征与不同数据集（如内部数据库）中的另一个特征进行关联的 ID。 允许的最大长度为 1000。|
|`categoryId`        |[category.Id](#category)      |是      | [`category`](#category) 特征的 ID。|
| `unitId`          | 字符串     | true     | 包含此特征的 [`unit`](#unit) 特征的 ID。 允许的最大长度为 1000。 |
| `isObstruction`          | boolean（默认值为 `null`。）| false     | 如果为 `true`，则此特征表示在通过包含的单元特征时要避开的障碍。 |
|`name` |    字符串 |    false |    以本地语言表示的特征的名称。 允许的最大长度为 1000。 |
|`nameSubtitle` |    字符串 |    false |   显示在特征的 `name` 下的副标题。 可用于以不同的语言显示名称等等。 允许的最大长度为 1000。 |
|`nameAlt` |    字符串 |    false |   用于该特征的替代名称。 允许的最大长度为 1000。 |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | 将特征表示为点的 [GeoJSON Point 几何图形](/rest/api/maps/wfs/get-feature-preview#featuregeojson)。 可用于定位特征的标签。|
|`obstructionArea` |   [多边形](/rest/api/maps/wfs/get-feature-preview#featuregeojson)| false | 行进时要避开的特征的简化几何图形（当线条几何图形较为复杂时）。 需要将 `isObstruction` 设置为 true。|

## <a name="areaelement"></a>areaElement

`areaElement` 是一个类特征，定义了单元中的多边形特征，如一个向下面开放的区域，单元中的岛屿等障碍物。

几何类型：MultiPolygon

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`originalId`        | 字符串     |true      | 从客户端数据派生的 ID。 允许的最大长度为 1000。|
|`externalId`        | 字符串     |true      | 客户端用于将特征与不同数据集（如内部数据库）中的另一个特征进行关联的 ID。 允许的最大长度为 1000。|
|`categoryId`        |[category.Id](#category)      |是      | [`category`](#category) 特征的 ID。|
| `unitId`          | 字符串     | true     | 包含此特征的 [`unit`](#unit) 特征的 ID。 允许的最大长度为 1000。 |
| `isObstruction`          | boolean | false     | 如果为 `true`，则此特征表示在通过包含的单元特征时要避开的障碍。 |
|`obstructionArea` |  几何图形：["Polygon","MultiPolygon" ]| false | 行进时要避开的特征的简化几何图形（当线条几何图形较为复杂时）。 需要将 `isObstruction` 设置为 true。|
|`name` |    字符串 |    false |    以本地语言表示的特征的名称。 允许的最大长度为 1000。 |
|`nameSubtitle` |    字符串 |    false |   显示在特征的 `name` 下的副标题。 可用于以不同的语言显示名称等等。  允许的最大长度为 1000。|
|`nameAlt` |    字符串 |    false |   用于该特征的替代名称。  允许的最大长度为 1000。|
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature-preview#featuregeojson) | false | 将特征表示为点的 [GeoJSON Point 几何图形](/rest/api/maps/wfs/get-feature-preview#featuregeojson)。 可用于定位特征的标签。|

## <a name="category"></a>category

`category` 类特征定义类别名称。 例如：“room.conference”。

几何类型：None

:::zone pivot="facility-ontology-v1"

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`originalId`        | 字符串     |true      | 从客户端数据派生的类别的原始 ID。 允许的最大长度为 1000。|
|`externalId`        | 字符串     |true      | 客户端用于将类别与不同数据集（如内部数据库）中的另一个类别进行关联的 ID。 允许的最大长度为 1000。|
|`name` |    字符串 |    true |   类别的名称。 建议使用“.”来表示类别的层次结构。 例如：“room.conference”、“room.privateoffice”。 允许的最大长度为 1000。 |
| `routeThroughBehavior` | boolean | false | 确定某个特征是否可用于直达交通。|
|`isRoutable`        | boolean（默认值为 `null`。）                  | false    |  确定某个特征是否应是路线图的一部分。 如果设置为 `true`，则单元可以在路线体验中用作源/目标或中间节点。 |

:::zone-end

:::zone pivot="facility-ontology-v2"

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`originalId`        | 字符串     |true      | 从客户端数据派生的类别的原始 ID。  允许的最大长度为 1000。|
|`externalId`        | 字符串     |true      | 客户端用于将类别与不同数据集（如内部数据库）中的另一个类别进行关联的 ID。 允许的最大长度为 1000。|
|`name` |    字符串 |    true |   类别的名称。 建议使用“.”来表示类别的层次结构。 例如：“room.conference”、“room.privateoffice”。 允许的最大长度为 1000。 |

:::zone-end
