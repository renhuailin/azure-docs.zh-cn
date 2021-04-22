---
title: 在通信服务中使用托管标识
titleSuffix: An Azure Communication Services quickstart
description: 通过托管标识，你可以授权 Azure 通信服务从 Azure VM、函数应用和其他资源中运行的应用程序进行访问。
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e4b71cc889615fd4784f11c9edd77b44ef421d9e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110712"
---
# <a name="use-managed-identities"></a>使用托管标识
通过使用托管标识开始使用 Azure 通信服务。 通信服务标识和 SMS SDK 支持通过 [Azure 资源托管标识](../../active-directory/managed-identities-azure-resources/overview.md)进行 Azure Active Directory (Azure AD) 身份验证。

本快速入门介绍如何通过支持托管标识的 Azure 环境授予对标识和 SMS SDK 的访问权限。 还介绍了如何在开发环境中测试代码。

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>后续步骤

- [深入了解 Azure 基于角色的访问控制](../../../articles/role-based-access-control/index.yml)
- [详细了解适用于 .NET 的 Azure 标识库](/dotnet/api/overview/azure/identity-readme)
- [创建用户访问令牌](../quickstarts/access-tokens.md)
- [发送短信](../quickstarts/telephony-sms/send.md)
- [了解有关短信的详细信息](../concepts/telephony-sms/concepts.md)
