---
title: 扩展 ontology
titleSuffix: Azure Digital Twins
description: 了解扩展本体的原因和策略
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 4051f8fe4b338f223b36c3a87ebcc3e3d3e51979
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114472966"
---
# <a name="extending-ontologies"></a>扩展 ontology 

行业标准[本体](concepts-ontologies.md)（例如[用于智能建筑的基于 DTDL 的 RealEstateCore 本体](https://github.com/Azure/opendigitaltwins-building)）非常适用于作为构建 IoT 解决方案的起点。 行业本体提供了一组丰富的基接口，这些接口专为域设计，并设计为可在 Azure IoT 服务（如 Azure 数字孪生）中开箱即用。 

但是，行业本体可能未涵盖解决方案的特定需求。 例如，可能需要将数字孪生体链接到存储在独立系统中的 3D 模型。 在这种情况下，可以扩展其中一个本体来添加自己的功能，同时保留原始本体的所有优势。

本文以基于 DTDL 的 [RealEstateCore](https://www.realestatecore.io/) 本体为基础示例，介绍使用新的 DTDL 属性扩展本体。 但是，此处所述的技术是通用的，可以结合任何 DTDL 功能（遥测、属性、关系、组件或命令）应用于基于 DTDL 的本体的任何部分。 

## <a name="realestatecore-space-hierarchy"></a>RealEstateCore 空间层次结构 

在基于 DTDL 的 RealEstateCore 本体中，空间层次结构可用于定义各种空间：房间、建筑物、区域等。空间层次结构从每个这样的模型中扩展，从而定义各种房间、建筑物和区域。 

层次结构的一部分如下图所示。 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-original.png" alt-text="流程图展示部分 RealEstateCore 空间层次结构。其中显示了空间、房间、会议室和办公室的元素。"::: 

有关 RealEstateCore 本体的详细信息，请参阅[采用行业标准本体](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology)。

## <a name="extending-the-realestatecore-space-hierarchy"></a>扩展 RealEstateCore 空间层次结构 

有时，行业本体未涵盖具有特定需求的解决方案。 在这种情况下，扩展层次结构便于继续使用行业本体，并根据需要进行自定义。 

本文介绍了两种不同的扩展本体层次结构的实用案例： 

* 为不在行业本体中的概念添加新的接口。 
* 将附加属性（或关系、组件、遥测或命令）添加到现有接口。

### <a name="add-new-interfaces-for-new-concepts"></a>为新概念添加新接口 

在此案例中，你想要为解决方案所需的概念添加接口，但行业本体中未提供这些接口。 例如，如果解决方案中存在基于 DTDL 的 RealEstateCore 本体中未表示的其他房间类型，则可以通过直接从 RealEstateCore 接口扩展来添加它们。 

下面的示例演示了一个解决方案，该解决方案需要表示 RealEstateCore 本体中不提供的“专注室”。 专注室是专为人们每次数小时专注一个任务而设计的一个小空间。 

若要使用此新概念扩展行业本体，请从行业本体中的接口[扩展](concepts-models.md#model-inheritance)，创建新的接口。 

添加专注室接口后，扩展的层次结构将显示新的房间类型。 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-1.png" alt-text="流程图展示部分 RealEstateCore 空间层次结构，包括新增内容。"::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>将附加功能添加到现有接口 

在此案例中，你想要将更多属性（或关系、组件、遥测或命令）添加到行业本体中的接口。

在本部分中，将看到两个示例： 
* 如果要构建的解决方案显示现有系统中已有的 3D 空间绘图，可能想要将每对数字孪生体与其 3D 绘图关联（按 ID），以便在解决方案显示有关空间的信息时，它还可以从现有系统检索 3D 绘图。 
* 如果解决方案需要跟踪会议室的联机/脱机状态，则可能需要跟踪会议室状态，以便在显示或查询中使用。 

这两个示例都可以使用新属性实现：`drawingId` 属性和 "online" 属性，前者将 3D 绘图与数字孪生体关联，后者指示会议室是否处于联机状态。 

通常，用户希望能够在未来的解决方案中将更新合并到本体中，因此不想要直接修改行业本体（这会覆盖添加内容）。 取而代之，可以从基于 DTDL 的 RealEstateCore 本体扩展自己的接口层次结构以在其中进行这种类型的添加。 所创建的每个接口都将使用多个接口继承，以从扩展的接口层次结构扩展其父 RealEstateCore 接口及其父接口。 利用此方法，可以将行业本体和添加内容结合使用。 

若要扩展行业本体，请通过从行业本体中的接口扩展来创建自己的接口，并为它们添加新功能。 为每个要扩展的接口创建一个新接口。 扩展接口以 DTDL 编写（参阅本文档后面“扩展接口的 DTDL”部分）。 

扩展如上所示的层次结构部分后，扩展层次结构如下图所示。 此时，扩展的“空间”接口添加 `drawingId` 属性，该属性包含将数字孪生体与 3D 绘图关联的 ID。 此外，“会议室”接口添加 "online" 属性，该属性包含会议室的联机状态。 通过继承，“会议室”接口包含 RealEstateCore“会议室”接口中的所有功能以及扩展“空间”接口中的所有功能。 

:::image type="content" source="media/concepts-ontologies-extend/real-estate-core-extended-2.png" alt-text="流程图展示扩展的 RealEstateCore 空间层次结构，包括所述的新增内容。"::: 

## <a name="using-the-extended-space-hierarchy"></a>使用扩展空间层次结构 

使用扩展空间层次结构创建数字孪生体时，每对数字孪生体的模型均来自扩展空间层次结构（而不是原始行业本体），且包括行业本体以及扩展接口中通过接口继承而获得的所有功能。

每对数字孪生体的模型将是扩展层次结构中的一个接口，如下图所示。 
 
:::image type="content" source="media/concepts-ontologies-extend/ontology-with-models.png" alt-text="流程图展示扩展的 RealEstateCore 空间层次结构，包括连接的模型空间、房间、会议室、办公室和专注室。"::: 

使用模型 ID（`IS_OF_MODEL` 运算符）查询数字孪生体时，应使用扩展层次结构中的模型 ID。 例如，`SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`。 

## <a name="contributing-back-to-the-original-ontology"></a>回馈到原始本体 

在某些情况下，需要采取广泛适用于本体大多数用户的方式扩展行业本体。 在这种情况下，应考虑将扩展回馈到原始本体。 每个本体都具有不同的回馈过程，因此请查看本体的 GitHub 存储库以详细了解回馈。 

## <a name="dtdl-for-new-interfaces"></a>新接口的 DTDL 

直接从行业本体扩展的新接口的 DTDL 如下所示。 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>扩展接口的 DTDL 

扩展接口的 DTDL（限于上述部分）如下所示。 

```json
[
  {
    "@id": "dtmi:com:example:Space;1",
    "@type": "Interface",
    "extends": "dtmi:digitaltwins:rec_3_3:core:Space;1",
    "contents": [
      {
        "@type": "Property",
        "name": "drawingid",
        "schema": "string"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Room;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:core:Room;1",
      "dtmi:com:example:Space;1"
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:ConferenceRoom;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:ConferenceRoom;1",
      "dtmi:com:example:Room;1"
    ],
    "contents": [
      {
        "@type": "Property",
        "name": "online",
        "schema": "boolean"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Office;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:Office;1", 
      "dtmi:com:example:Room;1" 
    ],
    "@context": "dtmi:dtdl:context;2" 
  }, 
  {
    "@id": "dtmi:com:example:FocusRoom;1", 
    "@type": "Interface", 
    "extends": "dtmi:com:example:Office;1", 
    "@context": "dtmi:dtdl:context;2" 
  }
]
``` 

## <a name="next-steps"></a>后续步骤

继续了解基于本体的模型开发路径：在模型开发路径中使用本体策略。