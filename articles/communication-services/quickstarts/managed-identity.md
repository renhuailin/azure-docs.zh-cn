---
title: 在通信服务中使用托管标识
titleSuffix: An Azure Communication Services quickstart
description: 通过托管标识，你可以授权 Azure 通信服务从 Azure VM、函数应用和其他资源中运行的应用程序进行访问。
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 05/27/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 3433aceb4bc9bda10c11602a7a45f28a5882f479
ms.sourcegitcommit: e832f58baf0b3a69c2e2781bd8e32d4f1ae932c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110585729"
---
# <a name="use-managed-identities"></a>使用托管标识
通过使用托管标识开始使用 Azure 通信服务。 通信服务标识和 SMS SDK 支持通过 [Azure 资源托管标识](../../active-directory/managed-identities-azure-resources/overview.md)进行 Azure Active Directory (Azure AD) 身份验证。

本快速入门介绍如何通过支持托管标识的 Azure 环境授予对标识和 SMS SDK 的访问权限。 还介绍了如何在开发环境中测试代码。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free)
- 有效的 Azure 通信服务资源，如果没有，请参阅[创建一个通信服务资源](./create-communication-resource.md)。
- 若要发送短信，需要一个[电话号码](./telephony-sms/get-phone-number.md)。
- 用于开发环境的设置托管标识，请参阅[使用托管标识授予访问权限](./managed-identity-from-cli.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>后续步骤

- [深入了解 Azure 基于角色的访问控制](../../../articles/role-based-access-control/index.yml)
- [详细了解适用于 .NET 的 Azure 标识库](/dotnet/api/overview/azure/identity-readme)
- [创建用户访问令牌](../quickstarts/access-tokens.md)
- [发送短信](../quickstarts/telephony-sms/send.md)
- [了解有关短信的详细信息](../concepts/telephony-sms/concepts.md)
