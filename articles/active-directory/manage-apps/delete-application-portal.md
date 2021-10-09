---
title: 快速入门：删除企业应用程序
description: 在 Azure Active Directory 中删除企业应用程序。
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
ms.openlocfilehash: 16bc055ed2b47ee5c212fa26387599a8d4ada7a8
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058186"
---
# <a name="quickstart-delete-an-enterprise-application-in-azure-active-directory"></a>快速入门：在 Azure Active Directory 中删除企业应用程序

在本快速入门中，使用 Azure Active Directory 管理中心删除添加到 Azure Active Directory (Azure AD) 租户的应用程序。

建议使用非生产环境来测试本快速入门中的步骤。

## <a name="prerequisites"></a>先决条件

若要删除企业应用程序，需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
- 完成[快速入门：添加企业应用程序](add-application-portal.md)中的步骤。

## <a name="delete-an-enterprise-application"></a>删除企业应用程序

删除企业应用程序：

1. 转到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)，并使用先决条件中列出的角色之一登录。
1. 在左侧菜单中，选择“企业应用程序”。 此时会打开“所有应用程序”窗格，其中显示了 Azure AD 租户中应用程序的列表。 搜索并选择要删除的应用程序。 例如“Azure AD SAML Toolkit 1”。
1. 在左侧菜单的“管理”部分中，选择“属性” 。
1. 在“属性”窗格的顶部，选择“删除”，然后选择“是”以确认要从 Azure AD 租户中删除该应用程序  。

    :::image type="content" source="media/delete-application-portal/delete-application.png" alt-text="删除企业应用程序。":::

## <a name="clean-up-resources"></a>清理资源

完成本快速入门系列后，请考虑删除应用程序以清理你的测试租户。 本快速入门介绍了如何删除应用程序。

## <a name="next-steps"></a>后续步骤

详细了解如何规划单一登录部署。
> [!div class="nextstepaction"]
> [计划单一登录部署](plan-sso-deployment.md)
