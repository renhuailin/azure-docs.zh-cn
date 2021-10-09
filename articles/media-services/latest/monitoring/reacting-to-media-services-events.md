---
title: 响应 Azure 媒体服务事件
description: 本文介绍如何使用 Azure 事件网格来订阅媒体服务事件。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 07/08/2021
ms.author: inhenkel
ms.openlocfilehash: ae414b91ef1c24df64d1201a5d171b065cdc268b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646035"
---
# <a name="handling-event-grid-events"></a>处理事件网格事件

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

媒体服务事件允许应用程序使用新式无服务器体系结构对不同事件（例如，作业状态更改事件）进行响应。 为此，它无需复杂的代码或高价低效的轮询服务。 相反，可以通过 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)向事件处理程序（如 [Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)），甚至是向自己的 Webhook 推送事件，且仅需为已使用的内容付费。 有关定价的详细信息，请参阅[事件网格定价](https://azure.microsoft.com/pricing/details/event-grid/)。

媒体服务事件的可用性与事件网格[可用性](../../../event-grid/overview.md)相关联，当事件网格在其他地区可用时，媒体服务事件也同样可用。  

## <a name="media-services-events-and-schemas"></a>媒体服务事件和架构

事件网格使用[事件订阅](../../../event-grid/concepts.md#event-subscriptions)将事件消息路由到订阅方。 媒体服务事件包含响应数据中的更改所需的所有信息。 可以识别媒体服务事件，因为 eventType 属性以“Microsoft.Media”开头。

有关详细信息，请参阅[媒体服务事件架构](../media-services-event-schemas.md)。

## <a name="samples-and-how-to"></a>示例和操作说明

媒体服务 [.NET 示例存储库](https://github.com/Azure-Samples/media-services-v3-dotnet)演示了如何使用最新的事件网格和事件中心客户端库在你自己的自定义应用程序中接收事件。

此外，以下操作说明文章演示了通过 CLI 和 Azure 门户使用事件网格。

* [监视事件 - 门户](../monitor-events-portal-how-to.md)
* [监视事件 - CLI](../job-state-events-cli-how-to.md)

## <a name="practices-for-consuming-events"></a>使用事件的做法

处理媒体服务事件的应用程序应遵循以下建议的做法：

* 由于可将多个订阅配置为将事件路由至相同的事件处理程序，因此请勿假定事件来自特定的源，而是应检查消息的主题，确保它来自所期望的存储帐户。
* 同样，检查 eventType 是否为准备处理的项，并且不假定所接收的全部事件都是期望的类型。
* 忽略不了解的字段。  此做法有助于适应将来可能添加的新功能。
* 使用“subject”前缀和后缀匹配项，将事件限制为特定事件。

> [!NOTE]
> 事件受事件网格[服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) 的约束。 若要使用 API 获取事件通知，请参阅相关示例，了解如何通过 [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) 或 [Java SDK](https://github.com/Azure-Samples/media-services-v3-java) 来使用事件。

## <a name="next-steps"></a>后续步骤

* [监视事件 - 门户](../monitor-events-portal-how-to.md)
* [监视事件 - CLI](../job-state-events-cli-how-to.md)
