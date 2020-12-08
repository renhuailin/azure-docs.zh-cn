---
title: '了解 Azure Active Directory 中应用的基于 OIDC 的单一登录 (SSO) '
description: 了解 Azure Active Directory 中应用的 (SSO) 的基于 OIDC 的单一登录。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperfq2
ms.openlocfilehash: 487bf3c83be16ee80838b3bbe8bf8532eb6ffe12
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780053"
---
# <a name="understand-oidc-based-single-sign-on"></a>了解基于 OIDC 的单一登录
在应用程序管理的 [快速入门系列](view-applications-portal.md) 中，已了解如何使用 Azure AD 作为标识提供程序 (IdP) 应用程序。 本文详细介绍了使用 OpenID Connect 标准实现单一登录的应用。 

## <a name="before-you-begin"></a>在开始之前
将应用添加到 Azure Active Directory 租户的过程取决于实现的应用程序的单一登录类型。 若要详细了解可用于身份管理 Azure AD 的应用的单一登录选项，请参阅 [单一登录选项](sso-options.md)。 本文介绍了基于 OIDC 的应用。


## <a name="basic-oidc-configuration"></a>基本 OIDC 配置
在 [快速入门系列](add-application-portal-setup-oidc-sso.md)中，有一篇文章介绍了如何配置单一登录。 在此示例中，你将了解如何将基于 OIDC 的应用添加到 Azure 租户。

添加使用 OIDC 标准进行单一登录的应用程序是一项很好的做法，那就是配置是最小的。 下面展示了如何将基于 OIDC 的应用添加到租户的简短视频。

在 Azure Active Directory 中添加基于 OIDC 的应用

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

若要了解有关用户和管理员同意的详细信息，请参阅 [了解用户和管理员同意](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)。

## <a name="next-steps"></a>后续步骤

- [应用程序管理的快速入门系列](add-application-portal-setup-oidc-sso.md)
- [单一登录选项](sso-options.md)
- [如何：使用多租户应用程序模式让任何 Azure Active Directory 用户登录](../develop/howto-convert-app-to-be-multi-tenant.md)
