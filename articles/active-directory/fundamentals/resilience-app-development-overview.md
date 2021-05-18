---
title: 提高你开发的身份验证和授权应用程序的复原能力
titleSuffix: Microsoft identity platform
description: 概要指导如何使用 Azure Active Directory 和 Microsoft 标识平台在应用程序开发中构建复原能力
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: 2076b2087c7cd90c83e1fe31c024d2c253109ee3
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108162178"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>提高你开发的身份验证和授权应用程序的复原能力

Microsoft 标识使用基于令牌的新式身份验证和授权。 这意味着客户端应用程序会从标识提供者处获取令牌，以对用户进行身份验证及授权该应用程序调用受保护的 API。 服务将会验证令牌。

令牌的有效期是一段时间，之后应用必须获取新的令牌。 由于网络或基础结构故障，或者由于身份验证服务中断等问题，对检索令牌的调用可能会失败；但这种情况很少发生。 在本文档中，我们概述了开发人员可执行哪些步骤来提高其应用程序在令牌获取失败的情况下的复原能力。

这些文章会指导你如何使用 Microsoft 标识平台和 Azure Active Directory 来提高应用中的复原能力。 其还会对客户端和服务应用程序以及守护程序应用程序提供指导，前者代表已登录的用户工作，后者代表其自身工作。 它们包含有关使用令牌和调用资源的最佳做法。

- [在将用户登录的应用程序中构建复原能力](resilience-client-app.md)
- [在没有用户的情况下将复原能力构建到应用程序中](resilience-daemon-app.md)
- [在标识和访问管理基础结构中构建复原能力](resilience-in-infrastructure.md)
- [在 CIAM 系统中构建复原能力](resilience-b2c.md)
- [生成在元数据刷新方面具有复原能力的服务](../develop/howto-build-services-resilient-to-metadata-refresh.md)
