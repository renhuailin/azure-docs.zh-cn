---
title: 提高你开发的守护程序应用程序中身份验证和授权的复原能力
titleSuffix: Microsoft identity platform
description: 有关使用 Microsoft 标识平台提高守护程序应用程序中身份验证和授权复原能力的指南
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: a7b8f893026bb96c8d768d2e6d07d0240ecb81fa
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724835"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-daemon-applications-you-develop"></a>提高你开发的守护程序应用程序中身份验证和授权的复原能力

本文提供了有关开发人员如何使用 Microsoft 标识平台和 Azure Active Directory 提高守护程序应用程序复原能力的指南。 这包括后台进程、服务、服务器到服务器应用以及不带用户的应用程序。

![向 Microsoft 标识发出调用的守护程序应用程序](media/resilience-daemon-app/calling-microsoft-identity.png)

## <a name="use-managed-identities-for-azure-resources"></a>使用 Azure 资源的托管标识

在 Microsoft Azure 上构建后台应用程序的开发人员可以使用 [Azure 资源的托管标识](../managed-identities-azure-resources/overview.md)。 托管标识使开发人员无需管理机密和凭据。 通过避免在证书过期、轮换或信任方面出现错误，此功能提高了复原能力。 它还具有一些专门用于提高复原能力的内置功能。

托管标识使用生存期较长的访问令牌和 Microsoft 标识中的信息，在现有令牌过期之前的较大时间范围内主动获取新令牌。 尝试获取新令牌时，应用可以继续运行。

托管标识还使用区域终结点来针对区域外故障提高性能和复原能力。 使用区域终结点有助于使所有流量都处于某个地理区域内。 例如，如果你的 Azure 资源处于 WestUS2 中，则所有流量（包括 Microsoft 标识生成的流量）都应保留在 WestUS2 中。 这样，通过整合服务的依赖项，便消除了可能的故障点。

## <a name="use-the-microsoft-authentication-library"></a>使用 Microsoft 身份验证库

不使用托管标识的守护程序应用开发人员可以使用 [Microsoft 身份验证库 (MSAL)](../develop/msal-overview.md)，该库使得实现身份验证和授权变得简单，并会自动使用用于复原的最佳做法。 MSAL 将使提供所需客户端凭据的过程变得更简单。 例如，在使用基于证书的凭据时，应用程序不需要实现创建 JSON Web 令牌断言以及对其进行签名。

### <a name="use-microsoftidentityweb-for-net-developers"></a>使用面向 .NET 开发人员的 Microsoft.Identity.Web

在 ASP.NET Core 上构建守护程序应用的开发人员可以使用 [Microsoft.Identity.Web](../develop/microsoft-identity-web.md) 库。 此库在 MSAL 的基础上构建，使得为 ASP.NET Core 应用实现授权更加容易。 它包括了多个[分布式令牌缓存](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization#distributed-token-cache)策略，以用于可在多个区域运行的分布式应用。

## <a name="cache-and-store-tokens"></a>缓存和存储令牌

如果未使用 MSAL 来实现身份验证和授权，则可以实施一些用于缓存和存储令牌的最佳做法。 MSAL 自动实施并遵循这些最佳做法。

应用程序会从标识提供程序获取用于授权该应用程序调用受保护 API 的令牌。 当你的应用接收令牌时，包含令牌的响应还会包含一个“expires\_in”属性，该属性告知应用程序可将令牌缓存并重复使用多长时间。 请务必让应用程序使用“expires\_in”属性来确定令牌的有效期。 应用程序绝对不得尝试对 API 访问令牌进行解码。 使用缓存的令牌可防止你的应用与 Microsoft 标识之间发生不必要的流量。 你的用户可以在该令牌的生存期内保持登录到你的应用程序。

## <a name="properly-handle-service-responses"></a>正确处理服务响应

最后，当应用程序应当处理所有错误响应时，某些响应可能会影响复原。 如果你的应用程序收到 HTTP 429 响应代码（请求过多），则 Microsoft 标识会限制你的请求。 如果你的应用继续发出太多请求，则会继续限制你的应用，阻止它接收令牌。 你的应用程序不应再次尝试获取令牌，直至经过“Retry-After”响应字段中的时间（以秒为单位）之后。 收到 429 响应通常表示应用程序未正确缓存并重复使用令牌。 开发人员应当查看令牌在应用程序中是如何缓存并重复使用的。

当应用程序收到 HTTP 5xx 响应代码时，应用不得进入快速重试循环。 如果收到 HTTP 5xx 响应代码，应用程序应遵循与对 429 响应相同的“Retry-After”处理。 如果响应未提供“Retry-After”标头，我们建议实施指数退避重试，第一次重试至少应比该响应晚 5 秒。

当请求超时时，应用程序不应立即重试。 应实施指数退避重试，第一次重试至少应比该响应晚 5 秒。

## <a name="next-steps"></a>后续步骤

- [在将用户登录的应用程序中构建复原能力](resilience-client-app.md)
- [在标识和访问管理基础结构中构建复原能力](resilience-in-infrastructure.md)
- [在 CIAM 系统中构建复原能力](resilience-b2c.md)