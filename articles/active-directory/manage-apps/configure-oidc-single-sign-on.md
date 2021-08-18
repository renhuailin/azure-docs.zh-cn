---
title: 了解基于 OIDC 的单一登录
titleSuffix: Azure AD
description: 了解 Azure Active Directory 中应用的基于 OIDC 的单一登录 (SSO)。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/19/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.custom: contperf-fy21q2
ms.openlocfilehash: 4cca31c844d4955df69a5fbdca450b67fd0f8bc9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738812"
---
# <a name="understand-oidc-based-single-sign-on"></a>了解基于 OIDC 的单一登录

在应用程序管理的[快速入门系列](view-applications-portal.md)中，你已了解如何使用 Azure AD 作为应用程序的标识提供者 (IdP)。 本文详细介绍了使用 OpenID Connect 标准实现单一登录的应用。

## <a name="before-you-begin"></a>在开始之前

将应用添加到 Azure Active Directory 租户的过程取决于实现的应用程序的单一登录类型。 若要详细了解关于可以使用 Azure AD 进行标识管理的应用的单一登录选项，请参阅[单一登录选项](sso-options.md)。 本文介绍了基于 OIDC 的应用。

## <a name="basic-oidc-configuration"></a>基本 OIDC 配置

在[快速入门系列](add-application-portal-setup-oidc-sso.md)中，有一篇文章介绍了如何配置单一登录。 在这篇文章中，你将了解如何将基于 OIDC 的应用添加到 Azure 租户。

添加使用 OIDC 标准进行单一登录的应用的好处是，只需进行极少的配置工作。 下面的简短视频展示了如何将基于 OIDC 的应用添加到租户。

在 Azure Active Directory 中添加基于 OIDC 的应用

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

若要详细了解用户和管理员同意，请参阅[了解用户和管理员同意](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)。

## <a name="next-steps"></a>后续步骤

- [应用程序管理的快速入门系列](add-application-portal-setup-oidc-sso.md)
- [单一登录选项](sso-options.md)
- [如何：使用多租户应用程序模式让任何 Azure Active Directory 用户登录](../develop/howto-convert-app-to-be-multi-tenant.md)
