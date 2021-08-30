---
title: 有关选择时序 ID 的最佳做法 - Azure 时序见解 | Microsoft Docs
description: 了解在 Azure 时序见解第 2 代中选择时序 ID 时的最佳做法。
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: seodec18
ms.openlocfilehash: 3a15b0c74ebe9a4696c2b61b6dd3b16d9f4b9d10
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730913"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>选择时序 ID 的最佳做法

本文总结了 Azure 时序见解第 2 代环境的时序 ID 的重要性，以及有关选择时序 ID 的最佳做法。

## <a name="choose-a-time-series-id"></a>选择时间序列 ID

选择适当的时序 ID 至关重要。 选择时序 ID 与为数据库选择分区键相同。 它在创建时序见解第 2 代环境时是必需的。

有关时序 ID 的详细说明，请观看环境预配教程。 你将看到两个不同的 JSON 遥测负载示例以及为每一个示例选择正确的时序 ID。</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]


> [!IMPORTANT]
> 时序 ID 如下：
>
> * 区分大小写的属性：搜索、比较和更新以及分区都会用到字母和字符的大小写。
> * 不可变属性：一旦创建，无法更改。

> [!TIP]
> 如果事件源是 IoT 中心，则时序 ID 可能是 iothub-connection-device-id。如果你计划使用 IoT 即插即用设备模型，或在不使用组件的情况下使用它们，则应将 dt-subject 作为组合键的一部分，以备将来需要时使用。

要遵守的主要最佳做法包括：

* 选择具有许多（例如，几百甚至几千个）非重复值的分区键。 在许多情况下，该键可能是 JSON 中的设备 ID、传感器 ID 或标记 ID。
* 在[时序模型](./concepts-model-overview.md)的叶节点级别，时序 ID 应是唯一的。
* 时序 ID 属性名称字符串的字符数限制为 128 个。 时序 ID 属性值的字符数限制为 1024 个。
* 如果时序 ID 的某个唯一属性值缺失，该值将被视为 null 值，并遵循相同的唯一性约束规则。
* 如果时序 ID 嵌套在复杂的 JSON 对象中，请确保在提供属性名称时遵循入口[平展规则](./concepts-json-flattening-escaping-rules.md)。 查看示例 [B](concepts-json-flattening-escaping-rules.md#example-b)。
* 此外，最多可以选择三个键属性作为时序 ID。 这些属性的组合将是表示时序 ID 的组合键。  
  > [!NOTE]
  > 三个键属性必须是字符串。
  > 必须针对此组合键运行查询，而不能每次查询一个属性。

## <a name="select-more-than-one-key-property"></a>选择多个键属性

以下方案描述如何选择多个键属性作为时序 ID。  

### <a name="example-1-time-series-id-with-a-unique-key"></a>示例 1：具有唯一键的时序 ID

* 你有两个传统的资产群。 每个群有一个唯一键。
* 其中一个群以属性 **deviceId** 进行唯一标识。 另一个群的唯一属性为 **objectId**。 这两个群都不包含对方的唯一属性。 在此示例中，可选择两个键 **deviceId** 和 **objectId** 作为唯一键。
* 我们接受 null 值，且如果事件有效负载中缺少某个属性，则计为 null 值。 向两个事件源发送数据时，这种方法同样合适，其中每个事件源中的数据具有唯一的时序 ID。

### <a name="example-2-time-series-id-with-a-composite-key"></a>示例 2：具有组合键的时序 ID

* 同一组资产中需要多个唯一的属性。
* 你是一家智能建筑制造商，希望在每个房间部署传感器。 对于每个房间，通常对 **sensorId** 使用相同的值。 例如，**sensor1**、**sensor2** 和 **sensor3**。
* 建筑物在属性 **flrRm** 中包含不同场地的重叠楼层号和房间号。 这些数字使用类似于 **1a**、**2b** 和 **3a** 的值。
* **location** 属性包含类似于 **Redmond**、**Barcelona** 和 **Tokyo** 的值。 若要实现唯一性，可将以下三个属性指定为时序 ID 键：**sensorId**、**flrRm** 和 **location**。

示例原始事件：

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

然后，可在 Azure 门户中输入组合键，如下所示：

[![配置环境的时序 ID。](media/v2-how-to-tsid/configure-environment-key.png)](media/v2-how-to-tsid/configure-environment-key.png#lightbox)

  > [!NOTE]
  > 在 Azure 门户中，请不要在一个文本框中输入以逗号分隔的属性名称，否则，它将被视为单个包含逗号的属性名称。
  > 请在每个属性名称自己的文本框中输入每个属性名称。

## <a name="next-steps"></a>后续步骤

* 阅读 [JSON 平展和转义规则](./concepts-json-flattening-escaping-rules.md)以了解事件将如何存储。

* 规划 [Azure 时序见解第 2 代环境](./how-to-plan-your-environment.md)。
