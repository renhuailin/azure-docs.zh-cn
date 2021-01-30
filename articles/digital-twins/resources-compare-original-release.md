---
title: 与原始版本的差异
titleSuffix: Azure Digital Twins
description: 了解新版本的 Azure 数字孪生中的更改内容
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8010581667354f2e8484bc7341227ec41713d10f
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072560"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>新版 Azure 数字孪生是指什么？ 它与原始版本 (2018) 有何不同？

Azure 数字孪生的第一个公共预览版已于 2018 年 10 月发布。 虽然原始版本中的核心概念已传输到当前服务，但许多接口和实现详细信息已经发生了变化，使服务更灵活和更易于访问。 这些更改是由客户反馈推动的。

> [!IMPORTANT]
> 就新服务的扩展功能而言，最初的 Azure 数字孪生服务已停用。 从2021年1月起，将不再提供其 Api 和关联的数据。

如果在首次公开预览期间使用第一版的 Azure 数字孪生，请使用本文中的信息和最佳做法，了解如何使用当前服务并利用其功能。

## <a name="differences-by-topic"></a>差异（按主题）

下图提供了在服务的原始版本和当前服务之间发生更改的概念的并排视图。

| 主题 | 初始版本 | 在当前版本中 |
| --- | --- | --- | --- |
| **建模**<br>*更灵活* | 原始版本是围绕智能空间设计的，因此它附带了大楼的内置词汇。 | 当前的 Azure 数字孪生与域无关。 可以为解决方案定义自己的自定义词汇和自定义模型，以更灵活的方式表示更多种环境。<br><br>在[概念：自定义模型](concepts-models.md)中了解详细信息。 |
| **拓扑**<br>*更灵活*| 原始版本支持为智能空间定制的树数据结构。 数字孪生与层次结构关系相关联。 | 在当前版本中，数字孪生可以连接到任意图形拓扑，并按需组织。 这使你可以更加灵活地表达现实世界中的复杂关系。<br><br>在[概念：数字孪生和孪生图](concepts-twins-graph.md)中了解详细信息。 |
| **计算**<br>*更丰富、更灵活* | 在原始版本中，用于处理事件和遥测的逻辑是在 (Udf) 的 JavaScript 用户定义函数中定义的。 使用 UDF 进行调试受到限制。 | 当前版本具有开放式计算模型：通过附加外部计算资源（如 [Azure Functions](../azure-functions/functions-overview.md)）来提供自定义逻辑。 这使你可以使用自己选择的编程语言、不受限制地访问自定义代码库，以及利用外部服务可能具有的开发和调试资源。<br><br>在[如何：设置用于处理数据的 Azure 函数](how-to-create-azure-function.md)中了解详细信息。 |
| **使用 IoT 中心进行设备管理**<br>*更易于访问* | 原始版本托管设备，其中包含 Azure 数字孪生服务内部的 [IoT 中心](../iot-hub/about-iot-hub.md) 实例。 开发人员无法完全访问此集成的中心。 | 在当前版本中，你通过将独立创建的 IoT 中心 (实例与它已管理) 的任何设备一起附加，来 "自带" IoT 中心。 这使你可以完全访问 IoT 中心的功能，并可以控制设备管理。<br><br>在[如何：从 IoT 中心引入遥测](how-to-ingest-iot-hub-data.md)中了解详细信息。 |
| **安全性**<br>*更标准* | 原始版本包含预定义的角色，可用于管理对实例的访问。 | 当前版本与相同的 [azure 基于角色的访问控制 ](../role-based-access-control/overview.md) 集成， (其他 azure 服务使用的 azure RBAC) 后端服务。 这可能使在解决方案中的其他 Azure 服务（如 IoT 中心、Azure Functions、事件网格等）之前进行身份验证变得更加简单。<br>使用 RBAC 时，仍可以使用预定义的角色，也可以构建和配置自定义角色。<br><br>在[概念：Azure 数字孪生解决方案的安全性](concepts-security.md)中了解详细信息。 |
| **伸缩性**<br>*更强大* | 原始版本具有设备、消息、图形和缩放单位的规模限制。 每个订阅仅支持一个 Azure 数字孪生实例。  | 当前版本依赖于新的体系结构，具有改进的可伸缩性，并且具有更高的计算能力。 它还支持每个区域每个订阅 10 个实例。<br><br>有关当前版本中的限制的详细信息，请参阅 [*Azure 数字孪生服务限制*](reference-service-limits.md) 。 |

## <a name="service-limits"></a>服务限制

有关 Azure 数字孪生限制的列表，请参阅 [*Azure 数字孪生服务限制*](reference-service-limits.md)。

## <a name="next-steps"></a>后续步骤

* 深入了解快速入门： [*快速入门：浏览示例方案*](quickstart-adt-explorer.md)中的当前版本。

* 或者，开始阅读有关概念的重要概念 [*：自定义模型*](concepts-models.md)。