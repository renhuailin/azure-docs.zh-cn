---
title: 什么是 IoT 连接器？ -Azure 医疗保健 Api
description: 在本文中，你将了解 IoT 连接器在将 IoMT 数据存储在 FHIR 服务中之前所执行的步骤。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 10/12/2021
ms.author: jasteppe
ms.openlocfilehash: 648b7356cb51a7c5f4444d4b707788f5b7bbf971
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005514"
---
# <a name="what-is-iot-connector"></a>什么是 IoT 连接器？

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

IoT 连接器是 Azure 医疗保健 Api 的一项可选功能，可提供从 (IoMT) 设备的医疗功能的 Internet 引入数据的功能。

本文提供 IoT 连接器中数据流的概述。 你将了解 IoT 连接器服务中的不同数据处理阶段，它将设备数据转换为快速医疗保健互操作性资源， (FHIR&#174; 基于) 的 [观察](https://www.hl7.org/fhir/observation.html) 资源。

下面是 IoT 连接器收到数据后经历的不同阶段。

## <a name="ingest"></a>引入

引入是将设备数据接收到 IoT 连接器服务的第一阶段。 设备数据的引入终结点托管在 Azure 事件中心内。 [Azure 事件中心](../../event-hubs/index.yml) 平台支持高规模和吞吐量，每秒可以接收和处理数百万条消息。 它还使 IoT 连接器服务能够在处理设备数据时异步使用消息，从而无需等待设备。

> [!NOTE]
> 对于设备数据，此时仅支持 JSON 格式。

## <a name="normalize"></a>规范化

规范化是下一个阶段，在此阶段中，从上述 Azure 事件中心检索设备数据，并使用设备映射模板进行处理。 此映射过程导致将设备数据转换为规范化的架构。 规范化过程不仅简化了以后的数据处理，而且还为将一条输入消息投影到多个规范化消息中而提供。 例如，设备可能会在单条消息中发送体温、脉搏率、血压和呼吸率等多个生命体征。 此输入消息将创建四个单独的 FHIR 资源。 每个资源都表示不同的生命体征，输入消息投射到四个不同的规范化消息中。

## <a name="group"></a>组

组是使用三个不同的参数对前一阶段中可用的规范化消息进行分组的下一阶段： 

* 设备标识
* 度量值类型 
* 时间段

设备标识和度量类型分组允许使用 [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) 度量类型。 此类型提供了一种简单的方法，可用于在 FHIR 中从设备表示基于时间的度量值，而时间段控制 IoT 连接器生成的观察资源写入 FHIR 服务的滞后时间。

> [!NOTE]
> 时间段值默认为 15 分钟，并且不能配置为预览。

## <a name="transform"></a>转换

在转换阶段，按 FHIR 目标映射模板处理分组规范化的消息。 与模板类型匹配的消息将转换为通过映射指定的基于 FHIR 的观察资源。

此时，还会使用消息中存在的设备标识符从 FHIR 服务检索设备资源及其关联的患者资源。 这些资源将作为对要创建的观察资源的引用添加。

> [!NOTE]
>所有标识查找会被解析后缓存，以减少 FHIR 服务的负载。 如果你计划重复使用多个患者的设备，则建议你创建一个特定于在消息有效负载中的患者和发送虚拟设备标识符的虚拟设备资源。 虚拟设备可以作为父级链接到实际设备资源。

如果 FHIR 服务中不存在给定设备标识符的设备资源，则结果取决于创建时设置的解决方法类型的值。 当设置为 "查找" 时，将忽略特定消息，管道将继续处理其他传入的消息。 如果设置为 "创建"，IoT 连接器服务将在 FHIR 服务上创建一个基本的设备和患者资源。

## <a name="persist"></a>保留

在转换阶段生成了 "观察" FHIR 资源后，资源将保存到 FHIR 服务。 如果 FHIR 资源是新资源，则会在服务中创建它。 如果 FHIR 资源已存在，则将更新该资源。

## <a name="next-steps"></a>后续步骤

有关 IoT 连接器映射的详细信息，请参阅以下指南：

>[!div class="nextstepaction"]
>[如何使用设备映射](how-to-use-device-mapping-iot.md)

>[!div class="nextstepaction"]
>[如何使用 FHIR 目标映射](how-to-use-fhir-mapping-iot.md)

 (FHIR&#174;) 是 [HL7](https://hl7.org/fhir/) 的注册商标，并与 HL7 的权限一起使用。