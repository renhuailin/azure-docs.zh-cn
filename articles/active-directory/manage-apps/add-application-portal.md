---
title: 快速入门：添加企业应用程序
description: 在 Azure Active Directory 中添加企业应用程序。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergleenl
ms.openlocfilehash: 66de4e18f8a6c88ae3c1504197eb6f39632c6425
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058147"
---
# <a name="quickstart-add-an-enterprise-application-in-azure-active-directory"></a>快速入门：在 Azure Active Directory 中添加企业应用程序

本快速入门介绍如何使用 Azure Active Directory 管理中心将企业应用程序添加到 Azure Active Directory (Azure AD) 租户。 Azure AD 有一个库，其中包含数千个预先集成的企业应用程序。 组织使用的许多应用程序可能已包含在库中。 本快速入门使用名为 Azure AD SAML Toolkit 的应用程序作为示例，但这些概念适用于库中的大多数[企业应用程序](../saas-apps/tutorial-list.md)。

建议使用非生产环境来测试本快速入门中的步骤。

## <a name="prerequisites"></a>先决条件

若要将企业应用程序添加到 Azure AD 租户，需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。

## <a name="add-an-enterprise-application"></a>添加企业应用程序

若要向租户添加企业应用程序，请执行以下操作：

1. 转到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)，并使用先决条件中列出的角色之一登录。
1. 在左侧菜单中，选择“企业应用程序”。 此时会打开“所有应用程序”窗格，其中显示了 Azure AD 租户中应用程序的列表。
1. 在“企业应用程序”窗格中，选择“新建应用程序” 。
1. “浏览 Azure AD 库”窗格随即将打开并显示云平台、本地应用程序和特别推荐应用程序的磁贴。 “特别推荐应用程序”部分中列出的应用程序带有指示其是否支持联合单一登录 (SSO) 和预配的图标。 搜索并选择应用程序。 本快速入门使用的是 Azure AD SAML Toolkit。

    :::image type="content" source="media/add-application-portal/browse-gallery.png" alt-text="在企业应用程序库中浏览要添加的应用程序。":::

1. 输入要用于识别应用程序实例的名称。 例如，`Azure AD SAML Toolkit 1`。
1. 选择“创建”  。

如果你选择安装的应用程序使用基于 OpenID Connect 的 SSO，那么，你看到的不是“创建”按钮，而是一个将你重定向到应用程序登录或注册页面（具体取决于你是否已有帐户）的按钮。 有关详细信息，请参阅[添加基于 OpenID Connect 的单一登录应用程序](add-application-portal-setup-oidc-sso.md)。 登录后，应用程序将添加到你的租户中。

## <a name="clean-up-resources"></a>清理资源

如果你打算完成下一个快速入门，请保留你创建的企业应用程序。 否则，可以考虑将其删除以清理租户。 有关详细信息，请参阅[删除应用程序](delete-application-portal.md)。

## <a name="next-steps"></a>后续步骤

了解如何创建用户帐户并将其分配给所添加的企业应用程序。
> [!div class="nextstepaction"]
> [创建和分配用户帐户](add-application-portal-assign-users.md)
