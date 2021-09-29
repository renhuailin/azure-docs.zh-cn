---
title: 应用程序同意操作的工作原理
description: 详细了解 Azure AD 同意框架的工作原理以便了解如何在 Azure AD上开发应用程序时使用该框架
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 2b68be9da11bc1f868b6bf30d3794ee6a3bc9ab6
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154197"
---
# <a name="how-application-consent-works"></a>应用程序同意操作的工作原理

本文旨在帮助读者深入了解 Azure AD 同意框架的工作原理以便更有效地开发应用程序。

## <a name="recommended-documents"></a>建议的文档

- 概括性了解：[允许资源所有者如何通过同意操作管理应用程序对资源的访问权限](./developer-glossary.md#consent)。
- 获取有关 [Azure AD 同意框架如何实现同意](./quickstart-register-app.md)的分步概述。
- 有关详细信息，请参阅[多租户应用程序如何使用同意框架](./howto-convert-app-to-be-multi-tenant.md)实现“用户”和“管理员”同意（支持更多高级多层应用程序模式）。
- 有关详细信息，请参阅[在授权代码授权流期间如何在 OAuth 2.0 协议层支持同意](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)

## <a name="next-steps"></a>后续步骤
[AzureAD Microsoft Q&A](/answers/topics/azure-active-directory.html)
