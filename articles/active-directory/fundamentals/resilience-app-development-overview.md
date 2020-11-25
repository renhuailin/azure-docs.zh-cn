---
title: 提高你开发的身份验证和授权应用程序的复原能力
titleSuffix: Microsoft identity platform
description: 使用 Azure Active Directory 和 Microsoft 标识平台开发应用程序开发的复原指南概述
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: c2c2f9d0ad7bfa50f543b57326b9fc8dab0069c6
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029296"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>提高你开发的身份验证和授权应用程序的复原能力

Microsoft 标识使用基于令牌的新式身份验证和授权。 这意味着，应用程序从标识提供者获取令牌，以便对用户进行身份验证，并授权应用程序调用受保护的 Api。

标记在应用必须获取新的时间段之前有效。 很少，由于网络或基础结构故障或身份验证服务中断等问题，对检索令牌的调用可能会失败。 在本文档中，我们概述了开发人员在发生令牌采集故障时可以采取的措施来提高应用程序的复原能力。

这些文章提供了有关使用 Microsoft 标识平台和 Azure Active Directory 在应用中提高复原能力的指导。 对于代表登录用户使用的客户端应用程序以及代表自己工作的后台程序应用程序，有一些指导。 它们包含使用令牌和调用资源的最佳实践。

- [为登录用户的应用程序构建复原能力](resilience-client-app.md)
- [无需用户即可在应用程序中构建复原能力](resilience-daemon-app.md)
- [在标识和访问管理基础结构中构建复原能力](resilience-in-infrastructure.md)
- [使用 Azure Active Directory B2C 在客户标识和访问管理中构建复原能力](resilience-b2c.md)
