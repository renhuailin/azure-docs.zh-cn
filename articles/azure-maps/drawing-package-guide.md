---
title: Microsoft Azure Maps Creator（预览）中的绘图包指南
description: 了解如何为 Azure Maps 转换服务准备绘图包
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: eb74ffb55536b18e48a4b5dcea83fac4db20abe0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727989"
---
# <a name="conversion-drawing-package-guide"></a>转换绘图包指南

本指南展示如何使用特定 CAD 命令为 [Azure Maps 转换服务](/rest/api/maps/v2/conversion)准备绘画包，以正确准备转换服务的 DWG 文件和清单文件。

首先，请确保绘图包采用 .zip 格式，并包含以下文件：

* 一个或多个 DWG 格式的绘图文件。
* 描述 DWG 文件和设施元数据的清单文件。

如果你没有自己的包用于与本指南一起参考，可以下载[示例绘制包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)。

可以选择任何 CAD 软件来打开和准备设施绘图文件。 但是，本指南是通过使用 Autodesk AutoCAD® 软件创建的。 本指南中引用的任何命令都应通过使用 Autodesk AutoCAD® 软件来执行。  

>[!TIP]
>有关本指南中未涵盖的绘图包要求的详细信息，请参阅[绘图包要求](drawing-requirements.md)。

## <a name="glossary-of-terms"></a>术语词汇表

为便于参考，以下是一些在阅读本指南时很重要的术语和定义。

| 术语  | 定义 |
|:-------|:------------|
| 层 | 绘图文件中的 AutoCAD DWG 层。|
| 实体 | 绘图文件中的 AutoCAD DWG 实体。 |
| 级别  |位于固定高度的建筑物区域。 例如，建筑物的地板。    |
| 功能 | 将几何图形与其他元数据信息合并在一起的对象。 |
| 特征类 | 特征的常见蓝图。 例如，*单元* 是特征类，而 *办公室* 是特征。 |

## <a name="step-1-dwg-file-requirements"></a>步骤 1：DWG 文件要求

为转换服务准备设施绘图文件时，请确保遵循以下初步要求和建议：

* 设施绘图文件必须以 DWG 格式保存，此格式是 Autodesk AutoCAD® 软件的本机文件格式。

* 转换服务与 AutoCAD DWG 文件格式一起使用。 AC1032 是 DWG 文件的内部格式版本，因此内部 DWG 文件格式版本最好选择  AC1032。

* DWG 文件只能包含单个层。 设施层必须在其自己单独的 DWG 文件中提供。  因此，如果设施有五层，则必须创建五个单独的 DWG 文件。

## <a name="step-2-prepare-the-dwg-files"></a>步骤 2：准备 DWG 文件

本指南的此部分将展示如何使用 CAD 命令来确保 DWG 文件满足转换服务的要求。

可以选择任何 CAD 软件来打开和准备设施绘图文件。 但是，本指南是通过使用 Autodesk AutoCAD® 软件创建的。 本指南中引用的任何命令都应通过使用 Autodesk AutoCAD® 软件来执行。  

### <a name="bind-external-references"></a>绑定外部引用

设施的每一层都必须作为一个 DWG 文件提供。 如果没有外部引用，则无需执行其他操作。 但是，如果存在任何外部引用，则必须将外部引用绑定到单个绘图。 若要绑定外部引用，可以使用 `XREF` 命令。 绑定后，每个外部引用绘图都将添加为块引用。 如果需要更改这些层中的任意一个，请记得使用 `XPLODE` 命令分解块引用。

### <a name="unit-of-measurement"></a>度量单位

可以使用任何度量单位创建绘图。 但是，所有绘图都必须使用相同的度量单位。 因此，如果设施的一层使用的是毫米，则所有其他层（绘图）也必须以毫米为单位。 可以使用 `UNITS` 命令验证或修改度量单位。

下图显示了 Autodesk AutoCAD® 软件中的“绘图单位”窗口，可用于验证度量单位。  

:::image border="true" type="content" source="./media/drawing-package-guide/units.png" alt-text="Autodesk AutoCAD® 软件中的“绘图单位”窗口":::

### <a name="alignment"></a>对齐方式

设施的每一层都作为单独的 DWG 文件提供。 因此，当每层堆叠在彼此之上时，可能没有完全对齐。 Azure Maps 转换服务要求所有绘图与物理空间对齐。 若要验证是否对齐，请使用可跨楼层的参考点，例如跨多个楼层的电梯或柱。 可以通过打开新绘图来查看所有楼层，然后使用 `XATTACH` 命令加载所有楼层绘图。 如果需要解决任何对齐问题，可以使用参考点和 `MOVE` 命令来重新对齐需要对齐的楼层。

### <a name="layers"></a>图层

确保绘图的每一层都包含一个特征类的实体。 如果某层包含用于墙的实体，则它不能具有其他特征，例如单元或门。  但是，可以在多个层上拆分特征类。 例如，绘图中可以含有三个包含墙实体的层。

此外，每个层都有一个支持的实体类型列表，并且忽略任何其他类型。 例如，如果单元标签层仅支持单行文本，则忽略同一层上的多行文本或折线。

如需更好地了解层和特征类，请参阅[绘图包要求](drawing-requirements.md)。

### <a name="exterior-layer"></a>Exterior 图层

从每个外部层或多个层创建单个级别特征。 此级别特征定义级别的周界。 请务必确保外部层中的实体满足层的要求。 例如，支持闭合折线；但不支持开放折线。 如果外部层由多个线段组成，则必须作为一条闭合折线提供。 若要将多个线段联接在一起，请选择所有线段并使用 `JOIN` 命令。

下图取自示例包，以红色显示设施的外部层。 单元层已关闭，以帮助实现可视化效果。

:::image border="true" type="content" source="./media/drawing-package-guide/exterior.png" alt-text="设施的外部层。":::

### <a name="unit-layer"></a>Unit 图层

单元是建筑物中可导航的空间，如办公室、走廊、楼梯和电梯。如果要表示每个单元，需要使用闭合实体类型（例如多边形、闭合折线、圆圈或闭合椭圆形）。 因此，只有墙和门不会创建单元，因为没有表示单元的实体。  

下图摘取自[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)，以红色显示单元标签层和单元层。 所有其他层已关闭，以帮助实现可视化效果。 此外，还选择了一个单元来帮助显示每个单元都是闭合的折线。  

:::image border="true" type="content" source="./media/drawing-package-guide/unit.png" alt-text="设施的单元层。":::

### <a name="unit-label-layer"></a>单元标签层

如果要向单元添加名称属性，则需要为单元标签添加单独的层。 标签必须作为单行文本实体提供，这些实体位于单元边界内。 必须将相应的单元属性添加到清单文件，其中 `unitName` 和文本内容匹配。  若要了解所有受支持的单元属性，请参阅 [`unitProperties`](#unitproperties)。

### <a name="door-layer"></a>Door 图层

门是可选的。 但是，如果要为单元指定入口点，则会用到门。 如果门是门层支持的实体类型，则可通过任何方式绘制门。 门必须与单元的边缘重叠，然后将单元的重叠边缘视为单元的开口。  

下图摘取自[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)，显示在单元边界上绘制有门（红色）的单元。

:::image border="true" type="content" source="./media/drawing-package-guide/door.png" alt-text="设施的门层。":::

### <a name="wall-layer"></a>Wall 图层

墙层旨在表示设施的物理区域，如墙和柱。 Azure Maps 转换服务将墙视为阻碍路线的物理结构。 考虑到这一点，应该将墙视为人可以看到但不可以通过的物理结构。 此层中不会捕获无法看到的任何内容。 如果墙内部有内墙或柱，则应该只捕获外墙。  

## <a name="step-3-prepare-the-manifest"></a>步骤 3：准备清单

绘图包清单是一个 JSON 文件。 清单告知 Azure Maps 转换服务如何读取设施 DWG 文件和元数据。 此类信息的一些示例可能是每个 DWG 层包含的特定信息或设施的地理位置。

若要实现成功转换，则必须定义所有“必需”属性。 可以在[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中找到示例清单文件。 本指南不介绍清单支持的属性。 有关清单属性的详细信息，请参阅[清单文件属性](drawing-requirements.md#manifest-file-requirements)。

### <a name="building-levels"></a>构建级别

构建级别指定哪个 DWG 文件用于哪个级别。 一个级别必须具有描述该级别的垂直顺序的级别名称和序号。 每个设施必须有一个序号 0，即设施的底层。 即使绘图占用了设备几层，也必须提供序号 0。 例如，楼层 15-17 可以分别定义为序号 0-2。

以下示例摘取自[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)。 设施有三层：地下室、底楼和第 2 层。 文件名包含文件的完整文件名和路径（相对于 .zip 绘图包中的清单文件）。  

```json
    "buildingLevels": { 
      "levels": [ 
       { 
           "levelName": "Basement", 
           "ordinal": -1, 
           "filename": "./Basement.dwg" 
            }, { 

            "levelName": "Ground", 
            "ordinal": 0, 
            "filename": "./Ground.dwg" 
            }, { 

            "levelName": "Level 2", 
            "ordinal": 1, 
             "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
```

### <a name="georeference"></a>georeference

`georeference` 对象用于指定设施在地理上的位置以及设施需要旋转的程度。 绘图的原点应与 `georeference` 对象提供的纬度和经度匹配。 真北与绘图的纵坐标轴 (Y) 之间的顺时针角度（度）。  

### <a name="dwglayers"></a>dwgLayers

`dwgLayers` 对象用于指定可在其中找到特征类的 DWG 层名称。 若要接收属性已转换的设施，请务必提供正确的层名称。 例如，DWG 墙层必须作为墙层提供，而不是作为单元层提供。 该绘图可以有其他层，如家具或管道；但是，如果未在清单中指定它们，Azure Maps 转换服务会忽略它们。  

以下是清单中的 `dwgLayers` 对象示例。  

```json
"dwgLayers": { 
        "exterior": [ 
            "OUTLINE" 
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
    } 
```

下图显示了 Autodesk AutoCAD® 软件中查看到的相应 DWG 绘图的层。

:::image border="true" type="content" source="./media/drawing-package-guide/layer.png" alt-text="Autodesk AutoCAD® 软件中的 DWG 层":::

### <a name="unitproperties"></a>unitProperties

`unitProperties` 对象使你能够定义 DWG 文件中无法定义的单元的其他属性。 例如，单元的目录信息或单元的类别类型。 通过将 `unitName` 对象与 `unitLabel` 层中的标签匹配，来将单元属性与单元关联。  

下图摘取自[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)。 它显示与清单中的单元属性关联的单元标签。

:::image border="true" type="content" source="./media/drawing-package-guide/unit-property.png" alt-text="将与清单中的单元属性关联的单元标签":::

以下代码片段显示了与单元关联的单元属性对象。  

```json
 "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
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
```

## <a name="step-4-prepare-the-drawing-package"></a>步骤 4：准备绘图包

现在应准备好所有 DWG 绘图，以满足 Azure Maps 转换服务要求。 还创建了一个清单文件来帮助描述设施。 所有文件都需要压缩到具有 `.zip` 扩展名的单个存档文件中。 请务必将清单文件命名为 `manifest.json`，并将其放置在压缩包的根目录中。 如果文件名包含清单的相对路径，则所有其他文件都可以放置在压缩包的任何目录中。 有关绘图包的示例，请参阅[示例绘图包](https://github.com/Azure-Samples/am-creator-indoor-data-examples)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：创建 Creator 室内定位](tutorial-creator-indoor-maps.md)
