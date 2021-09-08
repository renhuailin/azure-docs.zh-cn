---
title: 与原始版本的差异
titleSuffix: Azure Digital Twins
description: 了解新版本的 Azure 数字孪生中的更改内容
author: baanders
ms.author: baanders
ms.date: 8/24/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 551eae5e1d6ed5ee57e9753cbd487e9b7bd12277
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225744"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>新版 Azure 数字孪生是指什么？ 它与原始版本 (2018) 有何区别？

Azure 数字孪生的第一个公共预览版已于 2018 年 10 月发布。 虽然原始版本的核心概念现在已延续到当前服务，但是许多接口和实现细节都已更改，以使该服务更加灵活和可访问。 这些更改是由客户反馈推动的。

> [!IMPORTANT]
> 鉴于新服务的扩展功能，原始 Azure 数字孪生服务将停用。 自 2021 年 1 月起，将不再提供其 API 和关联的数据。

如果你在第一个公共预览版时使用了第一版的 Azure 数字孪生，请使用本文中的信息和最佳做法来了解如何使用当前服务并利用其功能。

## <a name="differences-by-topic"></a>差异（按主题）

下图提供了在原始版本的服务和当前服务之间已更改概念的并排视图。

| 主题 | 原始版本 | 当前版本 |
| --- | --- | --- | --- |
| **建模**<br>*更灵活* | 原始版本是围绕智能空间设计的，因此它附带了建筑物的内置词汇。 | 当前 Azure 数字孪生与域无关。 可以为解决方案定义自己的自定义词汇和自定义模型，以更灵活的方式表示更多种环境。<br><br>在[自定义模型](concepts-models.md)中了解详细信息。 |
| **拓扑**<br>*更灵活*| 原始版本支持针对智能空间量身定制的树数据结构。 数字孪生与层次结构关系相关联。 | 通过当前版本，数字孪生可以连接到任意图形拓扑，并根据需要进行组织。 这种自由使你可以更加灵活地表达现实世界中的复杂关系。<br><br>在[数字孪生和孪生图](concepts-twins-graph.md)中了解详细信息。 |
| **计算**<br>*更丰富、更灵活* | 在原始版本中，用于处理事件和遥测的逻辑是在 JavaScript 用户定义的函数 (UDF) 中进行定义的。 使用 UDF 进行调试受到限制。 | 当前版本具有开放式计算模型：通过附加外部计算资源（如 [Azure Functions](../azure-functions/functions-overview.md)）即可提供自定义逻辑。 此功能使你可以使用自己选择的编程语言、不受限制地访问自定义代码库，以及利用外部服务可能具有的开发和调试资源。<br><br>若要查看通过 Azure 函数的数据流所驱动的端到端方案，请参阅[连接端到端解决方案](tutorial-end-to-end.md)。 |
| **使用 IoT 中心进行设备管理**<br>*更易于访问* | 原始版本的托管设备，其中包含 Azure 数字孪生服务内部的 [IoT 中心](../iot-hub/about-iot-hub.md)实例。 开发人员无法完全访问此集成的中心。 | 在当前版本中，通过附加单独创建的 IoT 中心实例（以及它已管理的任何设备）即可“自带”IoT 中心。 此体系结构使你可以完全访问 IoT 中心的功能，并可以控制设备管理。<br><br>在[从 IoT 中心引入遥测](how-to-ingest-iot-hub-data.md)中了解详细信息。 |
| **安全性**<br>*更标准* | 原始版本具有预定义的角色，可使用这些角色来管理对实例的访问。 | 当前版本与其他 Azure 服务使用的相同 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 后端服务相集成。 此类集成可能使在解决方案中的其他 Azure 服务（如 IoT 中心、Azure Functions、事件网格等）之前进行身份验证变得更加简单。<br>使用 RBAC 时，仍可以使用预定义的角色，也可以构建和配置自定义角色。<br><br>在 [Azure 数字孪生解决方案的安全性](concepts-security.md)中了解详细信息。 |
| **伸缩性**<br>*更强大* | 原始版本对设备、消息、图形和缩放单元有缩放限制。 每个订阅仅支持一个 Azure 数字孪生实例。  | 当前版本依赖于可伸缩性得到提高的新体系结构，并且具有更强大的计算能力。 它还支持每个区域每个订阅 10 个实例。<br><br>有关当前版本中的限制的详细信息，请参阅 Azure 数字孪生服务限制。 |

## <a name="service-limits"></a>服务限制

有关 Azure 数字孪生限制的列表，请参阅 Azure 数字孪生服务限制。

## <a name="next-steps"></a>后续步骤

* 在 [Azure Digital Twins Explorer 入门](quickstart-azure-digital-twins-explorer.md)中深入了解如何使用当前版本。

* 或者，通过[自定义模型](concepts-models.md)，了解有关重要概念的信息。