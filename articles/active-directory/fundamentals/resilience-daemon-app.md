---
title: 提高身份验证和授权在你开发的守护程序应用程序中的复原能力
titleSuffix: Microsoft identity platform
description: 有关使用 Microsoft 标识平台在后台应用程序中提高身份验证和授权的指南
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: 74bfc9eeeb8375fca2c88a3fd3c31f17e130fc99
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919354"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-daemon-applications-you-develop"></a>提高身份验证和授权在你开发的守护程序应用程序中的复原能力

本文介绍了开发人员如何使用 Microsoft 标识平台和 Azure Active Directory 提高守护程序应用程序的复原能力。 这包括后台进程、服务、服务器到服务器应用以及不带用户的应用程序。

![向 Microsoft 标识发出调用的后台应用程序](media/resilience-daemon-app/calling-microsoft-identity.png)

## <a name="use-managed-identities-for-azure-resources"></a>使用 Azure 资源的托管标识

在 Microsoft Azure 上构建后台应用程序的开发人员可以使用 [Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。 托管标识使开发人员无需管理机密和凭据。 此功能通过避免在证书过期、旋转错误或信任方面出现错误，提高了复原能力。 它还具有一些专门用于提高复原能力的内置功能。

托管标识使用长生存期的访问令牌和 Microsoft 标识中的信息，在现有令牌过期之前，在较长时间内主动获取新令牌。 尝试获取新令牌时，应用可以继续运行。

托管标识还使用区域终结点来提高性能和复原能力，以防出现区域外故障。 使用区域终结点有助于将所有流量保存在某个地理区域内。 例如，如果你的 Azure 资源处于 WestUS2 中，则所有流量（包括 Microsoft 标识生成的流量）都应保留在 WestUS2 中。 这消除了服务依赖关系，从而消除了可能的故障点。

## <a name="use-the-microsoft-authentication-library"></a>使用 Microsoft 身份验证库

不使用托管标识的后台应用程序开发人员可以使用 [Microsoft 身份验证库 (MSAL) ](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)，这使得实现身份验证和授权变得简单，并自动使用最佳做法来实现复原。 MSAL 将使提供所需的客户端凭据的过程更简单。 例如，在使用基于证书的凭据时，应用程序不需要实现 JSON Web 令牌断言的创建和签名。

### <a name="use-microsoftidentityweb-for-net-developers"></a>使用面向 .NET 开发人员的 Microsoft web.config

生成后台应用程序的开发人员可使用[ASP.NET Core。](https://docs.microsoft.com/azure/active-directory/develop/microsoft-identity-web) 此库在 MSAL 的基础上构建，使 ASP.NET Core 应用更容易实现授权。 它包含可在多个区域运行的分布式应用程序的多个 [分布式令牌缓存](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#distributed-token-cache) 策略。

## <a name="cache-and-store-tokens"></a>缓存和存储令牌

如果未使用 MSAL 来实现身份验证和授权，则可以实现一些最佳做法来缓存和存储令牌。 MSAL 自动实现并遵循这些最佳做法。

应用程序从标识提供程序获取令牌，以授权应用程序调用受保护的 Api。 当你的应用程序接收令牌时，包含令牌的响应还会包含一个 "过期 \_ 时间" 属性，该属性告知应用程序缓存和重新使用令牌的时间长度。 应用程序必须使用 "过期时间 \_ " 属性来确定令牌的使用期限，这一点非常重要。 应用程序永远不能尝试对 API 访问令牌进行解码。 使用缓存的令牌可防止应用与 Microsoft 标识之间发生不必要的流量。 你的用户可以在该令牌的生存期内保持登录到你的应用程序。

## <a name="properly-handle-service-responses"></a>正确处理服务响应

最后，当应用程序应处理所有错误响应时，某些响应会影响复原能力。 如果你的应用程序收到 HTTP 429 响应代码，请求过多，Microsoft 标识会阻止你的请求。 如果应用继续发出太多请求，则会继续中止，阻止应用接收令牌。 应用程序不应再次尝试获取令牌，直到超过 "重试" 响应字段中的时间（以秒为单位）。 接收429响应通常表示应用程序未缓存并正确地使用令牌。 开发人员应查看如何缓存令牌并在应用程序中重复使用。

当应用程序收到 HTTP 5xx 响应代码时，应用程序不得进入快速重试循环。 当存在时，应用程序应遵循与429响应相同的 "重试后重试" 处理。 如果响应未提供 "重试" 标头，则建议使用第一次重试在响应之后的第一次重试来实现指数回退重试。

当请求超时时，应用程序不应立即重试。 在响应后，使用第一次重试至少5秒来实现指数回退重试。

## <a name="next-steps"></a>后续步骤

- [为登录用户的应用程序构建复原能力](resilience-client-app.md)
- [在标识和访问管理基础结构中构建复原能力](resilience-in-infrastructure.md)
- [在 CIAM 系统中构建复原能力](resilience-b2c.md)
