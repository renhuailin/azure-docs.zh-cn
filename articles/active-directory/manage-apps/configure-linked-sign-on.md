---
title: 向应用程序添加链接的单一登录
description: 在 Azure Active Directory 中向应用程序添加链接的单一登录。
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
ms.openlocfilehash: c8404b7ec361c90a6153cadc7ec6a71efb17fd1c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639615"
---
# <a name="add-linked-single-sign-on-to-an-application-in-azure-active-directory"></a>在 Azure Active Directory 中向应用程序添加链接的单一登录

本文介绍如何在 Azure Active Directory (Azure AD) 中为应用程序配置基于链接的单一登录 (SSO)。 基于链接的 SSO 使 Azure AD 能够向已在其他服务中为 SSO 配置的应用程序提供单一 SSO。 当用户在组织的“我的应用”或 Microsoft 365 门户中选择应用程序时，链接选项允许你配置目标位置。

基于链接的 SSO 不会通过 Azure AD 提供登录功能。 此选项仅设置用户在“我的应用”或 Microsoft 365 门户上选择应用程序时将用户发送到的位置。

基于链接的 SSO 有价值的一些常见场景包括：
- 添加一个链接，这个连接需指向当前使用联合的自定义 Web 应用程序，如 Active Directory 联合身份验证服务 (AD FS)。
- 添加指向你希望在用户访问页面上显示的特定网页的深层链接。
- 添加一个指向不需要身份验证的应用程序的链接。 链接选项不通过 Azure AD 凭据提供登录功能，但你仍然可以使用企业应用程序的一些其他功能。 例如，可以使用审核日志并可添加自定义徽标和应用程序名称。

## <a name="prerequisites"></a>先决条件

若要在 Azure AD 租户中配置基于链接的 SSO，你需要：
-   具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
-   以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
-   一个支持基于链接的 SSO 的应用程序。

## <a name="configure-linked-based-single-sign-on"></a>配置基于链接的单一登录

1.  使用适当的角色登录到 [Azure 门户](https://portal.azure.com)。
2.  在 Azure 服务中选择“Azure Active Directory”，然后选择“企业应用程序” 。
3.  搜索并选择要添加链接的 SSO 的应用程序。
4.  选择“单一登录”，然后选择“链接” 。
5.  输入应用程序的登录页的 URL。
6.  选择“保存”。 

## <a name="next-steps"></a>后续步骤

- [管理对应用的访问权限](what-is-access-management.md)