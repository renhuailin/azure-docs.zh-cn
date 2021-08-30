---
title: 服务限制
titleSuffix: Azure Digital Twins
description: 此图表显示了 Azure 数字孪生服务的限制。
author: baanders
ms.author: baanders
ms.date: 04/08/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: cb86fe0d4a99897821b389682c5a131ce0b9118c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114472863"
---
# <a name="azure-digital-twins-service-limits"></a>Azure 数字孪生服务限制

以下部分介绍 Azure 数字孪生的服务限制。

> [!NOTE]
> 此服务的限制在某些区域可调整。 下表的“可调整?”列中进行了相关描述。 当限制可调整时，“可调整?”的值为“是” 。
>
> 如果你的业务需要将可调整的限制或配额提高到默认限制之上，可以通过[创建支持票证](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)来请求更多资源。

## <a name="limits-by-type"></a>按类型列出的限制

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>处理限制

当达到某个限制时，服务将限制超出该限制的请求，这会导致这些请求返回 429 错误响应。

若要对此进行管理，下面是一些用于处理限制的建议。
* **使用重试逻辑。** [Azure 数字孪生 SDK](concepts-apis-sdks.md) 针对失败请求实现重试逻辑，因此如果你使用的是提供的 SDK，则此功能已内置其中。 否则请考虑在自己的应用程序中实现重试逻辑。 服务在失败响应中发送回 `Retry-After` 标头，可用于确定重试之前要等待的时间。
* **使用阈值和通知就接近限制发出警告。** Azure 数字孪生的一些服务限制具有相应的[指标](troubleshoot-metrics.md)，可用于跟踪这些领域的使用情况。 若要在达到阈值时配置阈值并针对任何指标设置警报，请参阅[故障排除：设置警报](troubleshoot-alerts.md)中的说明。 若要为不提供指标的其他限制设置通知，请考虑在自己的应用程序代码中实现此逻辑。
* **跨多个实例大规模部署。** 避免单点故障。 考虑跨多个实例按逻辑（例如按区域或租户）划分孪生体的子集，而不是将整个部署视为一个大大的整体。 

## <a name="next-steps"></a>后续步骤

在“服务概述”中详细了解 Azure 数字孪生的最新版本：
* [Azure 数字孪生是什么？](overview.md)
