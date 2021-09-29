---
title: 在通信服务中使用 Azure Active Directory
titleSuffix: An Azure Communication Services quickstart
description: 通过 Azure Active Directory，你可以授权 Azure 通信服务从 Azure VM、函数应用和其他资源中运行的应用程序进行访问。
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.subservice: identity
ms.topic: quickstart
ms.date: 06/30/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 39c1be0ddd7e2d80800faae96c42f983220307fb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677238"
---
# <a name="quickstart-authenticate-using-azure-active-directory"></a>快速入门：使用 Azure Active Directory 进行身份验证

通过使用 Azure Active Directory 开始使用 Azure 通信服务。 通信服务标识和 SMS SDK 支持 Azure Active Directory (Azure AD) 身份验证。

本快速入门介绍如何通过支持 Active Directory 的 Azure 环境授予对标识和 SMS SDK 的访问权限。 此外，还介绍如何通过为你的工作创建服务主体，在开发环境中测试代码。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free)
- 有效的 Azure 通信服务资源，如果没有，请参阅[创建一个通信服务资源](../create-communication-resource.md)。
- 若要发送短信，需要一个[电话号码](../telephony-sms/get-phone-number.md)。
- 用于开发环境的设置服务主体，请参阅[使用托管标识授予访问权限](./service-principal-from-cli.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/active-directory/service-principal-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/active-directory/service-principal-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/active-directory/service-principal-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/active-directory/service-principal-python.md)]
::: zone-end

## <a name="next-steps"></a>后续步骤

- [深入了解 Azure 基于角色的访问控制](../../../../articles/role-based-access-control/index.yml)
- [详细了解适用于 .NET 的 Azure 标识库](/dotnet/api/overview/azure/identity-readme)
- [创建用户访问令牌](../../quickstarts/access-tokens.md)
- [发送短信](../../quickstarts/telephony-sms/send.md)
- [了解有关短信的详细信息](../../concepts/telephony-sms/concepts.md)
- [快速创建标识进行测试](./quick-create-identity.md)。

