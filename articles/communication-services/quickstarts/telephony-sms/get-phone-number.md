---
title: 快速入门 - 使用 Azure 通信服务获取和管理电话号码
description: 了解如何使用 Azure 通信服务管理电话号码
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: pstn
ms.custom: references_regions
zone_pivot_groups: acs-azp-java-net-python-csharp-js
ms.openlocfilehash: a99f0d3aaf65ad7188f02c2c587653bd53f9948b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677105"
---
# <a name="quickstart-get-and-manage-phone-numbers"></a>快速入门：获取和管理电话号码

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

[!INCLUDE [Bulk Acquisition Instructions](../../includes/phone-number-special-order.md)]

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/phone-numbers-portal.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Azure portal](./includes/phone-numbers-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/phone-numbers-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/phone-numbers-python.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/phone-numbers-js.md)]
::: zone-end

## <a name="troubleshooting"></a>疑难解答

常见问题：

- 仅美国支持购买电话。 若要购买电话号码，请确保：
  - 关联的 Azure 订阅计费地址位于美国。 目前无法将资源移到另一个订阅。
  - 在“美国”数据位置预配了通信服务资源。 目前无法将资源移到另一个数据位置。

- 释放某电话号码后，要等到计费周期结束，才能释放或重新购买该电话号码。

- 删除某个通信服务资源的同时，将自动释放与该资源关联的电话号码。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何：

> [!div class="checklist"]
> * 购买电话号码
> * 管理电话号码
> * 释放电话号码

> [!div class="nextstepaction"]
> [发送短信](../telephony-sms/send.md)
> [呼叫入门](../voice-video-calling/getting-started-with-calling.md)
