---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: aedfe8783beacfe2e6679848ef4c2defa24d2da0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "95558911"
---
<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](../articles/governance/policy/overview.md#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
可以通过两种不同的资源来访问 Azure 认知服务：多服务资源或单一服务资源。

* 多服务资源：
    * 通过单个密钥和终结点访问多个 Azure 认知服务。
    * 合并来自你使用的服务的计费。
* 单服务资源：
    * 对于创建的每个服务，使用唯一密钥和终结点访问单个 Azure 认知服务。 
    * 使用免费层试用服务。