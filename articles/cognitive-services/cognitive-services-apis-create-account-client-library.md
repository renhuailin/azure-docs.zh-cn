---
title: 使用 Azure 管理客户端库创建认知服务资源
titleSuffix: Azure Cognitive Services
description: 使用 Azure 管理客户端库创建和管理 Azure 认知服务资源。
services: cognitive-services
keywords: 认知服务, 认知智能, 认知解决方案, ai 服务
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 06/04/2021
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 8f27b0e0bd94d2432c73039b75e28f839989dfde
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2021
ms.locfileid: "111539591"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>快速入门：使用 Azure 管理客户端库创建认知服务资源

按照本快速入门使用 Azure 管理客户端库创建和管理 Azure 认知服务资源。

Azure 认知服务是一个基于云的服务家族，它提供 REST API 和客户端库，帮助开发者将认知智能构建到其应用程序中。 开发者不需要直接的人工智能 (AI) 或数据科学技能或知识就能获得成功。 借助 Azure 认知服务，开发人员可以通过能够看、听、说、理解甚至开始推理的认知解决方案，轻松将认知功能添加到他们的应用程序中。

独立的 AI 服务由 Azure 订阅下创建的 Azure [资源](../azure-resource-manager/management/manage-resources-portal.md)表示。 创建资源后，可以使用生成的密钥和终结点对应用程序进行身份验证。

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end
