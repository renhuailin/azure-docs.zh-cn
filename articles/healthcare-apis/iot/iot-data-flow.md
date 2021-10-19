---
title: IoT 连接器中的数据流 - Azure 医疗保健 API
description: 了解 IoT 连接器的数据流。 IoT 连接器将 IoMT 数据提取、规范化、分组、转换和持久保存到 FHIR 服务。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: jasteppe
ms.openlocfilehash: 41ad8d284636e431b74c43006bb544f737ce1f47
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005496"
---
# <a name="iot-connector-data-flow"></a>IoT 连接器数据流

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本文概述了 IoT 连接器。 你将了解 IoT 连接器中将设备数据转换为基于 FHIR 快速医疗保健互操作性资源 (FHIR&#174;) 的不同 [数据处理](https://www.hl7.org/fhir/observation.html) 阶段。

下面是 IoT 连接器收到数据后经历的不同阶段。

## <a name="ingest"></a>引入
Ingest 是设备数据接收到 IoT 连接器的第一个阶段。 设备数据的引入终结点托管在 [Azure 事件中心](../../event-hubs/index.yml)内。 Azure 事件中心平台支持高规模和高吞吐量，每秒可以接收和处理数百万条消息。 它还使 IoT 连接器能够异步使用消息，无需设备在处理设备数据时等待。

> [!NOTE]
> 对于设备数据，此时仅支持 JSON 格式。

## <a name="normalize"></a>规范化
规范化是下一阶段，其中从上述 Azure 事件中心检索设备数据，然后使用设备映射进行处理。 此映射过程导致将设备数据转换为规范化的架构。 

规范化过程不仅简化了后续阶段中的数据处理，而且还提供了将一个输入消息投射到多个规范化消息的功能。 例如，设备可能会在单条消息中发送体温、脉搏率、血压和呼吸率等多个生命体征。 此输入消息将创建四个单独的 FHIR 资源。 每个资源都表示不同的生命体征，输入消息投射到四个不同的规范化消息中。

## <a name="group"></a>组
组是下一阶段，其中使用三个不同的参数对前一阶段可用的规范化消息进行分组：设备标识、度量类型和时间段。

设备标识和度量类型分组允许使用 [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) 度量类型。 此类型提供了一种简单的方法来表示 FHIR 的设备中基于时间的度量值。 时间段控制 IoT 连接器生成的观察资源写入 FHIR 服务的延迟。

> [!NOTE]
> 时间段值默认为 15 分钟，并且不能配置为预览。

## <a name="transform"></a>转换
在转换阶段，分组规范化消息通过 FHIR 目标映射模板进行处理。 与模板类型匹配的消息将转换为通过映射指定的基于 FHIR 的观察资源。

此时，[还会](https://www.hl7.org/fhir/device.html)使用消息中的设备标识符从 FHIR[](https://www.hl7.org/fhir/patient.html)服务中检索设备资源及其关联的患者资源。 这些资源将作为对要创建的观察资源的引用添加。

> [!NOTE]
> 所有标识查找在解析后进行缓存，以减少 FHIR 服务上的负载。 如果你计划对多个患者重复使用设备，最好创建一个特定于患者的虚拟设备资源，并在消息有效负载中发送虚拟设备标识符。 虚拟设备可以作为父级链接到实际设备资源。

如果 FHIR 服务中不存在给定设备标识符的设备资源，则结果取决于创建时设置的 `Resolution Type` 值。 如果设置为 `Lookup`，则忽略特定消息，管道将继续处理其他传入的消息。 如果设置为 ，IoT 连接器将在 FHIR 服务上创建一个裸机设备和患者 `Create` 资源。  

## <a name="persist"></a>保留
在转换阶段生成观察 FHIR 资源后，资源将保存到 FHIR 服务中。 如果 FHIR 资源是新的，将在 FHIR 服务上创建它。 如果 FHIR 资源已存在，则会进行更新。

## <a name="next-steps"></a>后续步骤

了解如何创建设备和 FHIR 目标映射。

> [!div class="nextstepaction"]
> [设备映射](how-to-use-device-mapping-iot.md)

> [!div class="nextstepaction"]
> [FHIR 目标映射](how-to-use-fhir-mapping-iot.md)

 (FHIR&#174;) 是 [HL7](https://hl7.org/fhir/) 的商标，并经 HL7 授权使用。