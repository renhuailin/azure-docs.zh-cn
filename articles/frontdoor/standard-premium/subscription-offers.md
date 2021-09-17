---
title: Azure Front Door Standard/Premium 订阅服务和带宽限制
description: 了解特定订阅类型的 Azure Front Door Standard/Premium 可用性。
services: front-door
author: duongau
manager: kumud
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 08/20/2021
ms.author: duau
ms.openlocfilehash: d98d69e616910d421b2b8bed5ea305039a0f13b2
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635209"
---
# <a name="azure-front-door-standardpremium-subscription-offers-and-bandwidth-throttling"></a>Azure Front Door Standard/Premium 订阅服务和带宽限制

根据订阅类型，Azure Front Door Standard/Premium 配置文件存在不同的带宽限制。

## <a name="free-and-trial-subscription"></a>免费试用版订阅

对于此类订阅，可以应用带宽限制。

## <a name="pay-as-you-go"></a>即用即付

在确定订阅处于正常状态并且具有足够的付款历史记录之前，将限制带宽。 在收到第一次付款后，系统会自动开始确定订阅状态和删除限制等过程。

如果你已付款，但系统仍未删除限制，则可[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，请求其删除限制。

## <a name="enterprise-agreements"></a>企业协议

企业协议订阅没有任何带宽限制。

## <a name="other-offer-types"></a>其他套餐类型

与即用即付相同的功能适用于以下类型的协议：

* Visual Studio
* MSDN
* 学生
* CSP

## <a name="next-steps"></a>后续步骤

了解如何[创建 Azure Front Door 标准版/高级版配置文件](create-front-door-portal.md)。
