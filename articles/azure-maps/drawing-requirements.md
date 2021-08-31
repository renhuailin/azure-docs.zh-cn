---
title: Microsoft Azure Maps Creator 中的绘图包要求
description: 了解绘图包要求，以便将设施设计文件转换为定位数据
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/02/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 2e6b380b040697a56179f5fec7f7d10796f035fd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746648"
---
# <a name="drawing-package-requirements"></a>绘图包要求

使用 [Azure Maps 转换服务](/rest/api/maps/v2/conversion)，可以将上传的绘图包转换为定位数据。 本文介绍了适用于转换 API 的绘图包要求。 若要查看示例包，可以下载示例[绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)。

有关如何准备绘图包的指南，请参阅[转换绘图包指南](drawing-package-guide.md)。


## <a name="prerequisites"></a>先决条件

绘图包中有以 DWG 格式保存的绘图，此格式是 Autodesk AutoCAD® 软件的本机文件格式。

可以选择任何 CAD 软件来生成绘图包中的绘图。  

[Azure Maps 转换服务](/rest/api/maps/v2/conversion)可以将绘图包转换为定位数据。 转换服务与 AutoCAD DWG 文件格式 `AC1032` 一起使用。


## <a name="glossary-of-terms"></a>术语词汇表

为便于参考，以下是一些在您阅读本文时很重要的术语和定义。

| 术语  | 定义 |
|:-------|:------------|
| 层 | 绘图文件中的 AutoCAD DWG 层。|
| 实体 | 绘图文件中的 AutoCAD DWG 实体。 |
| Xref  | 作为外部引用附加到主绘图的 AutoCAD DWG 文件格式文件。  |
| 级别 | 位于固定高度的建筑物区域。 例如，建筑物的地板。 |
| 特征 | 从转换服务生成的对象的实例，它将几何图形与元数据信息结合起来。 |
| 特征类 | 特征的常见蓝图。 例如，*单元* 是特征类，而 *办公室* 是特征。 |

## <a name="drawing-package-structure"></a>绘图包结构

绘图包是包含以下文件的 .zip 存档：

- AutoCAD DWG 文件格式的 DWG 文件。
- 描述绘图包中 DWG 文件的 _manifest.json_ 文件。

绘图包必须压缩到一个扩展名为 .zip 的存档文件中。 DWG 文件可以在压缩包中以任何方式组织，而清单文件则必须位于压缩包的根目录。 下面各部分详细介绍了 DWG 文件和清单文件的要求，以及这两种文件的内容。 若要查看示例包，可以下载[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)。

## <a name="dwg-file-conversion-process"></a>DWG 文件转换过程

[Azure Maps 转换服务](/rest/api/maps/v2/conversion)对每个 DWG 文件执行以下操作：

- 提取特征类：
    - Levels
    - 单位
    - 区域
    - Openings
    - Walls
    - 垂直滚动
- 生成“设施”功能。  
- 生成一组最小的默认类别特征，供其他特征引用：
    - 客房
    - structure
    - wall
    - opening.door
    - 区域
    - 设备
    
## <a name="dwg-file-requirements"></a>DWG 文件要求

设施的每个楼层都需要一个 DWG 文件。 单个楼层的所有数据都必须包含在一个 DWG 文件中。  所有外部引用 (Xref) 都必须绑定到父绘图。 例如，具有三个楼层的设施在绘图包中将包含三个 DWG 文件。

每个 DWG 文件都必须遵守以下要求：

- DWG 文件必须定义 Exterior 和 Unit 图层。 它可以视需要选择定义下列图层：Wall、Door、UnitLabel、Zone和 ZoneLabel。
- DWG 文件不能包含来自多个楼层的特征。
- DWG 文件不能包含来自多个设施的特征。
- DWG 必须与绘图包中的其他 DWG 文件引用相同的度量系统和度量单位。

## <a name="dwg-layer-requirements"></a>DWG 图层要求

每个 DWG 图层都必须遵循以下规则：

- 图层必须仅包含单个类的特征。 例如，units 和 walls 不能位于同一图层中。
- 单类特征可以由多个图层来表示。
- 允许自相交的多边形，但会自动修复。 修复后，[Azure Maps 转换服务](/rest/api/maps/v2/conversion)将会发出警告。 建议手动检查修复后的结果，因为它们可能与预期的结果不符。
- 每个图层都有一个支持的实体类型列表。 图层中的任何其他实体类型都会被忽略。 例如，wall 图层不支持文本实体。

下表概述了支持的实体类型和每个图层支持的映射特征。 如果图层包含不受支持的实体类型，则 [Azure Maps 转换服务](/rest/api/maps/v2/conversion)会忽略这些实体。  

| 层 | 实体类型 | 转换后的特征 |
| :----- | :-------------------| :-------
| [Exterior](#exterior-layer) | 多边形、折线（闭合）、圆、椭圆（闭合） | Levels
| [单位](#unit-layer) |  多边形、折线（闭合）、圆、椭圆（闭合） |  Units 和 Vertical Penetration
| [Wall](#wall-layer)  | 多边形、折线（闭合）、圆、椭圆（闭合）、结构 |
| [Door](#door-layer) | Polygon、PolyLine、Line、CircularArc、Circle | Openings
| [区域](#zone-layer) | 多边形、折线（闭合）、圆、椭圆（闭合） | 区域
| [UnitLabel](#unitlabel-layer) | Text（单行） | 不适用。 此图层只能向 Units 图层中的 unit 特征添加属性。 有关详细信息，请参阅 [UnitLabel 图层](#unitlabel-layer)。
| [ZoneLabel](#zonelabel-layer) | Text（单行） | 不适用。 此图层只能向 Zones 图层中的 zone 特征添加属性。 有关详细信息，请参阅“[区域标签图层](#zonelabel-layer)”。

下面各部分介绍了每个图层的要求。

### <a name="exterior-layer"></a>Exterior 图层

每个楼层的 DWG 文件都必须包含用于定义相应楼层的外围的图层。 此图层称为 *外* 图层。 例如，如果某个设施包含两个楼层，那么它需要有两个 DWG 文件，每个文件都有一个 Exterior 图层。

不论外图层中有多少实体绘图，[生成的设施数据集](tutorial-creator-indoor-maps.md#create-a-feature-stateset)对每个 DWG 文件都只包含一个楼层特征。 此外：

- 外图层必须绘制为多边形、折线（闭合）、圆。
- 外图层可能会重叠，但会消隐为一个几何图形。
- 生成的楼层特征必须至少为4平方米。
- 生成的楼层特征不得大于 400,000 平方米。

如果此图层包含多个重叠的折线，这些折线会消隐为一个楼层特征。 相反，如果此图层包含多个非重叠的折线，则生成的楼层特征就会有多多边形表示。

例如，可以将外图层看作是[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中的外形图层。

### <a name="unit-layer"></a>Unit 图层

每个楼层的 DWG 文件都应定义包含单元的图层。 单元是建筑物中可导航的空间，如办公室、走廊、楼梯和电梯。 如果 `VerticalPenetrationCategory` 定义了属性，则跨越多个楼层的导向性单元（如电梯和楼梯）将转换为垂直滚动特征。 相互重叠的垂直滚动特征被指定为一 `setid`。

Unit 图层应遵循以下要求：

- 单元图层必须绘制为多边形、折线（闭合）、圆或椭圆（闭合）。
- Unit 必须位于设施外围的边界内。
- Unit 不得部分重叠。
- Unit 不得包含任何自相交几何图形。

单元的命名方法为，在单元标记图层中创建一个文本对象，然后将此对象置于单元边界内。 有关详细信息，请参阅 [UnitLabel 图层](#unitlabel-layer)。

可以在[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中查看单元图层的示例。

### <a name="wall-layer"></a>Wall 图层

每个楼层的 DWG 文件都可能包含定义墙、柱和其他建筑结构的物理范围的图层。

- 墙图层必须绘制为多边形、折线（闭合）、圆或椭圆（闭合）。
- 一个或多个墙图层只能包含被解释为建筑结构的几何图形。

可以在[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中查看墙图层的示例。

### <a name="door-layer"></a>Door 图层

可以包括包含门的 DWG 图层。 每个门都必须与单元图层中单元的边缘重叠。

Azure Maps 数据集中的门开口表示为，与多个单元边界重叠的单线段。 以下图像示出了如何将门图层中的几何图形转换为数据集中的开口特征。

![显示开口生成步骤的四个图形](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Zone 图层

每个楼层的 DWG 文件都可能包含定义区域物理范围的区域图层。 区域是可以命名和呈现的不可导航空间。 区域可以跨多个级别，并使用 zoneSetId 属性组合在一起。

- 区域图层必须绘制为多边形、折线（闭合）或椭圆（闭合）。
- 区域可以重叠。
- 区域可能会位于设施外围的边界内，也可能会位于边界外。

区域的命名方法为，在区域标签图层中创建一个文本对象，然后将此文本对象置于区域边界内。 有关详细信息，请参阅 [ZoneLabel 图层](#zonelabel-layer)。

可以在[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中查看区域图层的示例。

### <a name="unitlabel-layer"></a>UnitLabel 图层

每个楼层的 DWG 文件都可能包含单元标签图层。 单元标签图层向从单元图层中提取的单元添加名字属性。 具有名字属性的单元可以在清单文件中指定其他详细信息。

- UnitLabel 必须是单行文本实体。
- UnitLabel 必须完全位于单元的边界内。
- 单元不得包含单元标签图层中的多个文本实体。

可以在[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中查看单元标签图层的示例。

### <a name="zonelabel-layer"></a>ZoneLabel 图层

每个楼层的 DWG 文件都可能包含区域标签图层。 此图层向从 Zone 图层中提取的区域添加 name 属性。 具有 name 属性的区域可以在清单文件中指定其他详细信息。

- ZoneLabel 必须是单行文本实体。
- ZoneLabel 必须位于区域的边界内。
- 区域不得包含区域标签图层中的多个文本实体。

可以在[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中查看区域标签图层的示例。

## <a name="manifest-file-requirements"></a>清单文件要求

zip 文件夹必须在目录的根级别包含命名为“manifest.json”的清单文件。 它描述了 DWG 文件，以便 [Azure Maps 转换服务](/rest/api/maps/v2/conversion)能够分析其内容。 只有由清单标识的文件才会被引入。 压缩文件夹中有、但在清单中没有正确列出的文件会被忽略。

清单文件 `buildingLevels` 对象中的文件路径必须相对于压缩文件夹的根。 DWG 文件名必须与设施楼层的名称完全匹配。 例如，“地下室”楼层的 DWG 文件为“Basement.dwg”。 “第 2 层”的 DWG 文件命名为“level_2.dwg”。 如果楼层名称中有空格，请使用下划线字符。

尽管有使用清单对象的要求，但并非所有对象都是必需的。 下表列出了 [Azure Maps 转换服务](/rest/api/maps/v2/conversion)版本 1.1 的必需和可选对象。

>[!NOTE]
> 除非另行规定，否则具有字符串属性类型的所有属性都可以有一千个字符。


| 对象 | 必选 | 说明 |
| :----- | :------- | :------- |
| `version` | 是 |清单架构版本。 目前仅支持版本 1.1。|
| `directoryInfo` | true | 概述设施地理位置和联系人信息。 它还可用于概述居用者的地理位置和联系人信息。 |
| `buildingLevels` | true | 指定建筑物的楼层，以及包含各楼层的设计的文件。 |
| `georeference` | true | 包含设施绘图的数值地理信息。 |
| `dwgLayers` | true | 列出图层名称，每个图层都列出自己的特征的名称。 |
| `unitProperties` | false | 可用于插入单元特征的其他元数据。 |
| `zoneProperties` | false | 可用于插入区域特征的其他元数据。 |

下面各部分详细介绍了每个对象的要求。

### `directoryInfo`

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
| `name`      | 字符串 | true   |  建筑物名称。 |
| `streetAddress`|    字符串 |    false    | 建筑物地址。 |
|`unit`     | 字符串    |  false    |  建筑物中的单元。 |
| `locality` |    字符串 |    false |    城市、城镇、区域、街坊或地区的名称。|
| `adminDivisions` |    字符串的 JSON 数组 |    false     | 一个包含地址标记的数组。 例如：(Country, State) 使用 ISO 3166 国家/地区代码和 ISO 3166-2 州/省/区域代码。 |
| `postalCode` |    字符串    | false    | 邮件分拣代码。 |
| `hoursOfOperation` |    字符串 |     false | 遵循 [OSM 开放时间](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification)格式。 |
| `phone`    | 字符串 |    false |    与建筑物关联的电话号码。 |
| `website`    | 字符串 |    false    | 与建筑物关联的网站。 |
| `nonPublic` |    bool    | false | 指定建筑物是否向公众开放的标志。 |
| `anchorLatitude` | numeric |    false | 设施定位点（图钉）的纬度。 |
| `anchorLongitude` | numeric |    false | 设施定位点（图钉）的经度。 |
| `anchorHeightAboveSeaLevel`  | numeric | false | 设施底层的海拔高度（以米为单位）。 |
| `defaultLevelVerticalExtent` | numeric | false | 在楼层的 `verticalExtent` 未定义时使用的、此设施的楼层的默认高度（厚度）。 |

### `buildingLevels`

`buildingLevels` 对象包含建筑物楼层的 JSON 数组。

| properties  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`levelName`    |字符串    |true |    楼层的描述性名称。 例如：1 楼、大厅、蓝色停车场、地下室等。|
|`ordinal` | integer |    true | 确定各楼层的垂直顺序。 每个设施都必须有序号为 0 的楼层。 |
|`heightAboveFacilityAnchor` | numeric | false |    底层以上的楼层高度（米）。 |
| `verticalExtent` | numeric | false | 楼层的地板到天花板高度（厚度）。 |
|`filename` |    字符串 |    true |    建筑物楼层的 CAD 绘图的文件系统路径。 它必须相对于建筑物的 zip 文件的根。 |

### `georeference`

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`lat`    | numeric |    true |    设施绘图的原点的纬度（用十进制表示）。 坐标原点必须位于 WGS84 Web Mercator (`EPSG:3857`)。|
|`lon`    |numeric|    true|    设施绘图的原点的经度（用十进制表示）。 坐标原点必须位于 WGS84 Web Mercator (`EPSG:3857`)。 |
|`angle`|    numeric|    true|   真北与绘图的纵坐标轴 (Y) 之间的顺时针角度（度）。   |

### `dwgLayers`

| 属性  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`exterior`    |字符串数组|    是|    定义建筑物外表面轮廓的图层的名称。|
|`unit`|    字符串数组|    是|    定义单元的图层的名称。|
|`wall`|    字符串数组    |false|    定义墙的图层的名称。|
|`door`    |字符串数组|    false   | 定义门的图层的名称。|
|`unitLabel`    |字符串数组|    false    |定义单元名称的图层的名称。|
|`zone` | 字符串数组    | false    | 定义区域的图层的名称。|
|`zoneLabel` | 字符串数组 |     false |    定义区域名称的图层的名称。|

### `unitProperties`

`unitProperties` 对象包含 unit 属性的 JSON 数组。

| properties  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|`unitName`    |字符串    |true    |要与此 `unitProperty` 记录关联的单元的名称。 只有当在多个 `unitLabel` 图层中找到与 `unitName` 匹配的标签时，此记录才有效。 |
|`categoryName`|    字符串|    false    |单元的用途。 [此处](https://atlas.microsoft.com/sdk/javascript/indoor/0.1/categories.json)提供了所提供呈现样式可使用的值列表。 |
|`occupants`    |directoryInfo 对象的数组 |false    |单元的居用者列表。 |
|`nameAlt`|    字符串|    false|    单元的备用名称。 |
|`nameSubtitle`|    字符串    |false|    单元的副标题。 |
|`addressRoomNumber`|    字符串|    false|    单元的房间、单元、公寓或套房编号。|
|`verticalPenetrationCategory`|    字符串|    false| 如果定义了此属性，则生成的特征是 Vertical Penetration (VRT)，而不是单元。 Vertical Penetration 可用于导航到它上面或下面的楼层中的其他 Vertical Penetration 特征。 Vertical penetration 是[类别](https://aka.ms/pa-indoor-spacecategories)名称。 如果定义了此属性，则`categoryName`该属性将被 `verticalPenetrationCategory` 重写。 |
|`verticalPenetrationDirection`|    字符串|    false    |如果定义了 `verticalPenetrationCategory`，则可以视需要选择定义有效的行进方向。 允许的值有 `lowToHigh`、`highToLow`、`both` 和 `closed`。 默认值为 `both`。 该值区分大小写。|
| `nonPublic` | bool | false | 指明单元是否向公众开放。 |
| `isRoutable` | bool | false | 如果将此属性设置为 `false` ，则不能通过单元进行切换。 默认值为 `true`。 |
| `isOpenArea` | bool | false | 允许导航代理进入单元，而无需将开口附加到单元。 默认情况下，此值设置为 `true`“没有开口的单元”，或者设置为 `false`“带有开口的单元”。 如果在没有开口的单元上将 `isOpenArea` 手动设置为 `false`，则会出现警告，因为导航代理无法访问结果单元。|

### `zoneProperties`

`zoneProperties` 对象包含 zone 属性的 JSON 数组。

| properties  | 类型 | 必需 | 说明 |
|-----------|------|----------|-------------|
|zoneName        |字符串    |true    |要与 `zoneProperty` 记录关联的区域的名称。 只有当在区域的 `zoneLabel` 图层中找到与 `zoneName` 匹配的标签时，此记录才有效。  |
|categoryName|    字符串|    false    |区域的用途。 [此处](https://atlas.microsoft.com/sdk/javascript/indoor/0.1/categories.json)提供了所提供呈现样式可使用的值列表。|
|zoneNameAlt|    字符串|    false    |区域的备用名称。  |
|zoneNameSubtitle|    字符串 |    false    |区域的副标题。 |
|zoneSetId|    字符串 |    false    | 设置 ID 以在多个区域之间建立关系，以便可以将它们作为一个组来查询或选择。 例如，跨越多个楼层的区域。 |

### <a name="sample-drawing-package-manifest"></a>示例绘图包清单

下面是示例绘图包的示例清单文件。 若要下载整个绘图包，请单击[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)。

#### <a name="manifest-file"></a>清单文件

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddress": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

在绘图包符合要求后，可以使用 [Azure Maps 转换服务](/rest/api/maps/v2/conversion)将绘图包转换为定位数据集。 然后，可以使用此数据集和“室内定位”模块来生成室内定位。

> [!div class="nextstepaction"]
> [Creator Facility Ontology](creator-facility-ontology.md)

> [!div class="nextstepaction"]
> [适用于室内定位的 Creator](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [绘图包指南](drawing-package-guide.md)

> [!div class="nextstepaction"]
>[用于室内地图的 Creator](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [教程：创建 Creator 室内定位](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [室内定位的动态样式](indoor-map-dynamic-styling.md)