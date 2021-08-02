---
title: Azure CDN 订阅套餐和带宽限制
description: 了解为特定订阅类型提供的 Azure CDN。
services: cdn
author: duongau
manager: kumud
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.author: duau
ms.openlocfilehash: 9c8cf3576075b2854b14d83d126e9f790d47b74d
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112008140"
---
# <a name="azure-cdn-subscription-offers-and-bandwidth-throttling"></a>Azure CDN 订阅套餐和带宽限制

某些 Azure CDN 产品/服务仅适用于某些订阅类型。 此外，还可以根据订阅类型应用带宽限制。

## <a name="free-and-trial-subscription"></a>免费试用版订阅

Microsoft 的 Azure CDN 是适用于试用版订阅的唯一 CDN 产品/服务。 对于此类订阅，可以应用带宽限制。  
 
## <a name="pay-as-you-go"></a>即用即付

Akamai 的 Azure CDN 不适用于即用即付订阅。   
 
对于 Microsoft 和 Verizon 的 Azure CDN，在确定订阅处于正常状态并且具有足够的付款历史记录之前，可以限制带宽。 在收到第一次付款后，系统会自动开始确定订阅状态和删除限制等过程。   
 
如果你已付款，但系统仍未删除限制，则可[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，请求其删除限制。 
  
## <a name="enterprise-agreements"></a>企业协议

所有 Azure CDN 均适用于企业协议订阅。 企业协议订阅没有任何带宽限制。  
 
## <a name="other-offer-types"></a>其他套餐类型
 
与即用即付相同的功能适用于以下类型的协议：

* Visual Studio 
* MSDN
* 学生
* CSP

## <a name="next-steps"></a>后续步骤

* 了解如何[创建 Azure CDN 配置文件](cdn-create-new-endpoint.md)。
