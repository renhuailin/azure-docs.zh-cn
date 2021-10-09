---
title: 添加基于 OpenID Connect 的单一登录应用程序
description: 了解如何在 Azure Active Directory 中添加基于 OpenID Connect 的单一登录应用程序。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 24d30dc81b2b2a3da2ac5f172dabe77cf5b01f9c
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058224"
---
# <a name="add-an-openid-connect-based-single-sign-on-application-in-azure-active-directory"></a>在 Azure Active Directory 中添加基于 OpenID Connect 的单一登录应用程序

将支持基于 [OpenID Connect (OIDC)](../develop/active-directory-v2-protocols.md) 的单一登录 (SSO) 的应用程序添加到 Azure Active Directory (Azure AD) 租户中。

建议使用非生产环境来测试本页面中的步骤。

## <a name="prerequisites"></a>先决条件

若要配置基于 OIDC 的 SSO，需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。

## <a name="add-the-application"></a>添加应用程序

添加使用 OIDC 标准来实现 SSO 的企业应用程序时，请选择设置按钮。 选择该按钮即表示你完成了应用程序的注册过程。

若要为应用程序配置基于 OIDC 的 SSO，请执行以下操作：

1. 转到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)，并使用先决条件中列出的角色之一登录。
1. 在左侧菜单中，选择“企业应用程序”。 此时会打开“所有应用程序”窗格，其中显示了 Azure AD 租户中应用程序的列表。 
1. 在“企业应用程序”窗格中，选择“新建应用程序” 。
1. “浏览 Azure AD 库”窗格随即将打开并显示云平台、本地应用程序和特别推荐应用程序的磁贴。 “特别推荐应用程序”部分中列出的应用程序带有指示其是否支持联合 SSO 和预配的图标。 搜索并选择应用程序。 本示例使用的是 SmartSheet。
1. 选择“注册”。 使用来自 Azure Active Directory 的用户帐户凭据登录。 如果你已经订阅该应用程序，则会验证用户详细信息和租户信息。 如果该应用程序无法验证用户，它会将你重定向到应用程序服务注册页面。

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/oidc-sso-configuration.png" alt-text="填写应用程序的同意屏幕。":::

1. 依次选择“代表组织同意”和“接受” 。 系统将应用程序添加到租户中，并显示应用程序主页。 若要详细了解用户和管理员同意，请参阅[了解用户和管理员同意](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)。

## <a name="next-steps"></a>后续步骤

详细了解如何规划单一登录部署。
> [!div class="nextstepaction"]
> [计划单一登录部署](plan-sso-deployment.md)
