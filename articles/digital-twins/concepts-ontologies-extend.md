---
title: 扩展本体论
titleSuffix: Azure Digital Twins
description: 了解扩展 ontology 的原因和策略
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e5973f58887b212919ad739232faafddcf9e735c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561288"
---
# <a name="extending-ontologies"></a>扩展本体论 

行业标准 [ontology](concepts-ontologies.md)（例如， [DTDL 的 RealEstateCore ontology for smart 大楼](https://github.com/Azure/opendigitaltwins-building)）是开始构建 IoT 解决方案的好办法。 行业本体论提供了一组丰富的基本接口，这些接口专为你的域设计，并设计为可在 Azure IoT 服务（如 Azure 数字孪生）中使用。 

但是，您的解决方案可能有业界 ontology 未涵盖的特定需求。 例如，你可能想要将数字孪生链接到存储在单独系统中的3D 模型。 在这种情况下，你可以扩展其中的一个本体论来添加你自己的功能，同时保留原始 ontology 的所有优势。

本文使用基于 DTDL 的 [RealEstateCore](https://www.realestatecore.io/) ontology 作为使用新的 DTDL 属性扩展本体论的示例。 但是，此处所述的技术是通用的，可以应用于基于 DTDL 的 ontology 的任何部分，其中包含任何 DTDL 功能 (遥测、属性、关系、组件或命令) 。 

## <a name="realestatecore-space-hierarchy"></a>RealEstateCore 空间层次结构 

在基于 DTDL 的 RealEstateCore ontology 中，空间层次结构用于定义各种类型的空间：房间、建筑物、区域等。该层次结构从每个模型中进行扩展以定义各种房间、建筑物和区域。 

层次结构的一部分如下图所示。 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-original.png" alt-text="说明 RealEstateCore 空间层次结构一部分的流程图。在顶级，有一个名为 Space 的元素;它通过 &quot;扩展&quot; 箭头向下移动到房间;房间通过两个 &quot;扩展&quot; 箭头连接到 ConferenceRoom 和 Office。"::: 

有关 RealEstateCore ontology 的详细信息，请参阅 [*概念：采用行业标准本体论*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology)。

## <a name="extending-the-realestatecore-space-hierarchy"></a>扩展 RealEstateCore 空间层次结构 

有时，您的解决方案具有业界 ontology 未涵盖的特定需求。 在这种情况下，扩展层次结构使您可以继续使用行业 ontology，并根据需要进行自定义。 

本文介绍两种不同的情况，在这种情况下，扩展 ontology 的层次结构很有用： 

* 为不在行业 ontology 中的概念添加新的接口。 
* 将附加属性 (或关系、组件、遥测或命令) 添加到现有接口。

### <a name="add-new-interfaces-for-new-concepts"></a>为新概念添加新接口 

在这种情况下，你想要为你的解决方案所需的概念添加接口，但这不是在行业 ontology 中提供的。 例如，如果你的解决方案具有在基于 DTDL 的 RealEstateCore ontology 中未表示的其他类型的聊天室，则可以通过直接从 RealEstateCore 接口扩展来添加它们。 

下面的示例演示了一个解决方案，该解决方案需要表示 RealEstateCore ontology 中不存在的 "焦点房间"。 焦点房间是一小部分，旨在让人们一次将精力集中在一个任务上。 

若要使用这一新概念扩展行业 ontology，请创建新的接口，该接口从行业 ontology 中的接口进行 [扩展](concepts-models.md#model-inheritance) 。 

添加焦点房间界面后，扩展的层次结构将显示新的房间类型。 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-1.png" alt-text="说明上述 RealEstateCore 空间层次结构的流程图，其中包含新的添加。在 ConferenceRoom 和 Office 的底层，有一个名为 FocusRoom 的新元素 (也通过房间中的 &quot;扩展&quot; 箭头进行连接) "::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>将其他功能添加到现有接口 

在这种情况下，你想要将更多属性 (或关系、组件、遥测或命令) 添加到行业 ontology 中的接口。

在本部分中，你将看到两个示例： 
* 如果要构建的解决方案显示已在现有系统中使用的3D 绘图，你可能想要将每个数字源与其3D 绘图 (按 ID) ，以便在解决方案显示有关空间的信息时，它还可以从现有系统检索3D 绘图。 
* 如果你的解决方案需要跟踪会议室的联机/脱机状态，则可能需要跟踪会议室状态，以便在显示或查询中使用。 

这两个示例都可以使用新属性实现：一个 `drawingId` 属性，该属性将三维绘图与数字克隆相关联，并使用 "online" 属性来指示会议室是否处于联机状态。 

通常情况下，你不希望直接修改行业 ontology，因为你希望能够在未来的解决方案中将更新合并到解决方案中 (这会覆盖你的添加) 。 相反，可以在自己的接口层次结构中进行这种类型的添加，该层次结构从基于 DTDL 的 RealEstateCore ontology 进行扩展。 你创建的每个接口都将使用多个接口继承从扩展接口层次结构扩展其父 RealEstateCore 接口及其父接口。 利用此方法，您可以使用行业 ontology，并将添加到一起。 

若要扩展行业 ontology，请创建自己的接口，该接口从行业 ontology 中的接口进行扩展，并将新功能添加到扩展接口。 对于要扩展的每个接口，创建一个新接口。 扩展接口以 DTDL 编写 (参见本文档后面的 DTDL for Extended 接口部分，) 。 

扩展了如上所示的层次结构部分后，扩展层次结构如下图所示。 此时，扩展的空间接口将添加 `drawingId` 一个属性，该属性将包含一个将数字整数与三维绘图关联的 ID。 此外，ConferenceRoom 接口将添加一个 "online" 属性，该属性将包含会议室的联机状态。 通过继承，ConferenceRoom 接口包含 RealEstateCore ConferenceRoom 接口中的所有功能，以及来自扩展空间接口的所有功能。 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-2.png" alt-text="说明上述扩展 RealEstateCore 空间层次结构的流程图，并提供更多新内容。房间现在与 Space 元素共享其级别，后者将与 &quot;扩展&quot; 箭头向下连接到 ConferenceRoom 和 Office 旁边的新房间元素。 新元素通过更多的 &quot;扩展&quot; 关系连接到现有的 ontology。"::: 

## <a name="using-the-extended-space-hierarchy"></a>使用扩展空间层次结构 

使用扩展空间层次结构创建数字孪生时，每个数字克隆的模型将是从扩展空间层次结构中 (，而不是原始行业 ontology) ，它将包括行业 ontology 中的所有功能和通过接口继承的扩展接口。

每个数字克隆的模型将是扩展层次结构中的一个接口，如下图所示。 
 
:::image type="content" source="media/concepts-extending-ontologies/ontology-with-models.png" alt-text="扩展的 RealEstateCore 空间层次结构的摘录，其中包括空间 (顶层) 、一个房间 (中间级别) 和 ConferenceRoom、Office 和 FocusRoom (低级别) 。模型名称连接到每个元素 (例如，房间连接到名为 Room101) 的模型。"::: 

使用 (运算符) 的模型 ID 查询数字孪生时 `IS_OF_MODEL` ，应使用扩展层次结构中的模型 id。 例如，`SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`。 

## <a name="contributing-back-to-the-original-ontology"></a>参与到原始 ontology 

在某些情况下，你将以广泛适用于 ontology 大多数用户的方式扩展行业 ontology。 在这种情况下，应考虑将扩展发布回原始 ontology。 每个 ontology 都有不同的处理过程，因此请查看 ontology 的 GitHub 存储库以获取贡献详细信息。 

## <a name="dtdl-for-new-interfaces"></a>新接口的 DTDL 

直接从行业 ontology 扩展的新接口的 DTDL 将如下所示。 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>扩展接口的 DTDL 

扩展接口的 DTDL （限于上述部分）将如下所示。 

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

继续使用基于本体论开发模型的路径： [*在模型开发路径中使用 ontology 策略*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)。