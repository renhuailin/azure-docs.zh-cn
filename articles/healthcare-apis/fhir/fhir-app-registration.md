---
title: 注册Azure Active Directory应用Azure API for FHIR
description: 本教程介绍需要注册哪些应用程序Azure API for FHIR Azure 的 FHIR 服务器。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.reviewer: dseven
ms.author: cavoeg
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: d170c7e081d7e176fa1a0b17dd16c8c1e90867c8
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112283476"
---
# <a name="register-the-azure-active-directory-apps-for-azure-api-for-fhir"></a>注册Azure Active Directory应用Azure API for FHIR

设置 Azure API for FHIR 或适用于 Azure 的 FHIR Server (OSS) 时，有几个配置选项可供选择。 对于开放源代码，你需要创建自己的资源应用程序注册。 对于Azure API for FHIR，此资源应用程序会自动创建。

## <a name="application-registrations"></a>应用程序注册

为了使应用程序能够与 Azure AD 进行交互，需要对其进行注册。 在 FHIR 服务器的上下文中，有两种应用程序注册类型需要讨论：

1. 资源应用程序注册。
1. 客户端应用程序注册。

**资源** 应用程序是 API 或Azure AD资源（具体而言，使用 Azure AD 保护的 API 或资源Azure API for FHIR。 预配服务Azure API for FHIR会自动创建一个资源应用程序，但如果使用的是开源服务器，则需要在 Azure AD 中注册资源应用程序。 [](register-resource-azure-ad-client-app.md) 此资源应用程序将具有标识符 URI。 建议此 URI 与 FHIR 服务器的 URI 相同。 此 URI 应用作 `Audience` FHIR 服务器的 。 客户端应用程序在请求令牌时可以请求访问此 FHIR 服务器。

*客户端* 应用程序是请求令牌的客户端的注册。 通常，在 OAuth 2.0 中，我们区分至少三种不同类型的应用程序：

1. **机密客户端**，也称为 Azure AD。 机密客户端是使用授权 [代码流](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) 代表提供有效凭据的已登录用户获取令牌的应用程序。 它们被称为机密客户端，因为它们能够保存机密，并且将在交换令牌的身份验证代码Azure AD向用户呈现此机密。 由于机密客户端能够使用客户端机密自行进行身份验证，因此它们比公共客户端更受信任，并且可以具有更长的令牌，并被授予刷新令牌。 阅读有关如何注册 [机密客户端 的详细信息](register-confidential-azure-ad-client-app.md)。 请注意，注册客户端将接收授权代码的回复 URL 非常重要。
1. **公共客户端**。 这些是无法保留机密的客户端。 这通常是移动设备应用程序或单页 JavaScript 应用程序，用户可以发现客户端中的机密。 公共客户端也使用授权代码流，但在获取令牌时不允许它们提供机密，并且它们可能有较短的长期令牌，并且没有刷新令牌。 阅读有关如何注册 [公共客户端 的详细信息](register-public-azure-ad-client-app.md)。
1. 服务客户端。 这些客户端代表自己获取 (，而不是代表使用) [流 的用户。](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) 它们通常表示以非交互方式访问 FHIR 服务器的应用程序。 例如，一个"ingestion"过程。 使用服务客户端时，不需要启动通过调用终结点获取令牌 `/authorize` 的过程。 服务客户端可以直接转到 `/token` 终结点，并提供客户端 ID 和客户端机密以获取令牌。 阅读有关如何注册 [服务客户端的详细信息](register-service-azure-ad-client-app.md)

## <a name="next-steps"></a>后续步骤

本概述介绍了使用 FHIR API 可能需要的应用程序注册类型。

根据你的设置，请参阅注册应用程序操作指南

* [注册资源应用程序](register-resource-azure-ad-client-app.md)
* [注册机密客户端应用程序](register-confidential-azure-ad-client-app.md)
* [注册公共客户端应用程序](register-public-azure-ad-client-app.md)
* [注册服务应用程序](register-service-azure-ad-client-app.md)

注册应用程序后，可以部署Azure API for FHIR。

>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-powershell-quickstart.md)