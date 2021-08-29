---
title: 概念：Azure API for FHIR 的适用于 FHIR 的 Azure IoT 连接器（预览版）功能中的数据流
description: 了解适用于 FHIR 的 Azure IoT 连接器（预览版）的数据流。 适用于 FHIR 的 Azure IoT 连接器（预览版）可引入、规范化、分组、转换 IoMT 数据，以及将 IoMT 数据持久保存到 Azure API for FHIR。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: rabhaiya
ms.openlocfilehash: db46af2dffbecb6301af13bbabd871c1c5392354
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779749"
---
# <a name="azure-iot-connector-for-fhir-preview-data-flow"></a>适用于 FHIR 的 Azure IoT 连接器（预览版）数据流

本文概述了适用于快速医疗保健互操作性资源 (FHIR&#174;) 的 Azure IoT 连接器* 中的数据流。 你将了解适用于 FHIR 的 Azure IoT 连接器中用于将设备数据转换为基于 FHIR 的[观察](https://www.hl7.org/fhir/observation.html)资源的不同数据处理阶段。

![适用于 FHIR 的 Azure IoT 连接器数据流](media/concepts-iot-data-flow/iot-connector-data-flow.png)

上图显示了使用适用于 FHIR 的 Azure IoT 连接器的常见数据流。 

下面是数据在被适用于 FHIR 的 Azure IoT 连接器接收后经历的各个阶段。

## <a name="ingest"></a>引入
引入是将设备数据接收到适用于 FHIR 的 Azure IoT 连接器的第一阶段。 设备数据的引入终结点托管在 [Azure 事件中心](../../event-hubs/index.yml)。 Azure 事件中心平台支持高规模和高吞吐量，每秒可以接收和处理数百万条消息。 它还允许适用于 FHIR 的 Azure IoT 连接器以异步方式使用消息，这样就无需在处理设备数据的同时让设备等待。

> [!NOTE]
> 对于设备数据，此时仅支持 JSON 格式。

## <a name="normalize"></a>规范化
规范化是下一个阶段，此阶段从上述 Azure 事件中心检索设备数据，并使用设备映射模板进行处理。 此映射过程导致将设备数据转换为规范化的架构。 

规范化过程不仅简化了后续阶段中的数据处理，而且还提供了将一条输入消息投射到多条规范化消息的功能。 例如，设备可能会在单条消息中发送体温、脉搏、血压和呼吸频率等多个生命体征。 此输入消息会创建四个单独的 FHIR 资源。 每个资源都表示不同的生命体征，输入消息投射到四条不同的规范化消息。

## <a name="group"></a>组
组是下一阶段，将使用三个不同的参数对前一阶段提供的规范化消息进行分组：设备标识、度量类型和时间段。

设备标识和度量类型分组允许使用 [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) 度量类型。 此类型提供了一种简单的方法来表示 FHIR 的设备中一系列基于时间的度量。 时间段控制适用于 FHIR 的 Azure IoT 连接器生成的观察资源写入 Azure API for FHIR 的延迟。

> [!NOTE]
> 时间段值默认为 15 分钟，不能配置它来进行预览。

## <a name="transform"></a>转换
在转换阶段，通过 FHIR 映射模板处理分组的规范化消息。 与模板类型匹配的消息将转换为通过映射指定的基于 FHIR 的观察资源。

此时，还会使用消息中的设备标识符从 FHIR 服务器检索[设备](https://www.hl7.org/fhir/device.html)资源及其关联的[患者](https://www.hl7.org/fhir/patient.html)资源。 这些资源将添加为对要创建的观察资源的引用。

> [!NOTE]
> 所有标识查找在解析后都会缓存，以减少 FHIR 服务器上的负载。 如果你打算将设备重复用于多个患者，则建议创建一个特定于患者的虚拟设备资源，并在消息有效负载中发送虚拟设备标识符。 虚拟设备可以作为父级链接到实际设备资源。

如果 FHIR 服务器中不存在给定设备标识符的设备资源，则结果取决于创建时设置的 `Resolution Type` 值。 如果设置为 `Lookup`，则忽略特定消息，管道将继续处理其他传入消息。 如果设置为 `Create`，则适用于 FHIR 的 Azure IoT 连接器将在 FHIR 服务器上创建基本的设备和患者资源。  

## <a name="persist"></a>保留
在转换阶段生成 FHIR 观察资源后，资源将保存到 Azure API for FHIR 中。 如果 FHIR 资源是新资源，则会在服务器上创建该资源。 如果 FHIR 资源已存在，则会进行更新。

## <a name="next-steps"></a>后续步骤

单击下面的“下一步”以了解如何创建设备和 FHIR 映射模板。

>[!div class="nextstepaction"]
>[适用于 FHIR 的 Azure IoT 连接器映射模板](iot-mapping-templates.md)

*在 Azure 门户中，适用于 FHIR 的 Azure IoT 连接器称为 IoT Connector（预览版）。 FHIR 是 HL7 的注册商标，经 HL7 许可使用。 