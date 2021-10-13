---
title: 快速入门：查看企业应用程序
description: 查看已注册使用 Azure Active Directory 租户的企业应用程序。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 09/07/2021
ms.author: davidmu
ms.reviewer: arvinh
ms.openlocfilehash: 0f744176e3a1472cadd740f94e34839bb7a8a09d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613712"
---
# <a name="quickstart-view-enterprise-applications-in-azure-active-directory"></a>快速入门：在 Azure Active Directory 中查看企业应用程序

本快速入门介绍如何使用 Azure Active Directory 管理中心搜索并查看已在 Azure Active Directory (Azure AD) 租户中配置的企业应用程序。

建议使用非生产环境来测试本快速入门中的步骤。

## <a name="prerequisites"></a>先决条件

若要查看已在 Azure AD 租户中注册的应用程序，你需要：

- 一个 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
- 完成[快速入门：添加企业应用程序](add-application-portal.md)中的步骤。

## <a name="view-a-list-of-applications"></a>查看应用程序列表

查看在租户中注册的企业应用程序，方法如下：

1. 转到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)，并使用先决条件中列出的角色之一登录。
1. 在左侧菜单中，选择“企业应用程序”。 此时会打开“所有应用程序”窗格，其中显示了 Azure AD 租户中应用程序的列表。

    :::image type="content" source="media/view-applications-portal/view-enterprise-applications.png" alt-text="查看 Azure AD 租户中已注册的应用程序。":::

1. 若要查看更多应用程序，请选择列表底部的“加载更多”。 如果租户中有大量应用程序，则也许搜索特定应用程序要比滚动浏览整个列表容易些。

## <a name="search-for-an-application"></a>搜索应用程序

搜索特定的应用程序：

1. 在“应用程序类型”菜单中，选择“所有应用程序”，然后选择“应用”  。
1. 输入要查找的应用程序的名称。 如果该应用程序已添加到 Azure AD 租户中，则会显示在搜索结果中。 例如，可以搜索在以前的快速入门中使用的 Azure AD SAML Toolkit 1 应用程序。 
1. 尝试输入应用程序名称的头几个字母。

## <a name="select-viewing-options"></a>选择查看选项

根据要查找的内容选择所需的选项：

1. 可以根据“应用程序类型”、“应用程序状态”和“应用程序可见性”查看应用程序。  
1. 在“应用程序类型”下选择下述选项之一：
    - “企业应用程序”显示非 Microsoft 应用程序。
    - “Microsoft 应用程序”显示 Microsoft 应用程序。
    - “所有应用程序”显示非 Microsoft 应用程序和 Microsoft 应用程序。
1. 在“应用程序状态”下选择“任何”、“已禁用”或“已启用”。    “任何”选项包括已禁用和已启用的应用程序。
1. 在“应用程序可见性”下选择“任何”或“已隐藏”  。 “已隐藏”选项显示租户中存在的、但对用户不可见的应用程序。
1. 选择所需的选项后，选择“应用”。

## <a name="clean-up-resources"></a>清理资源

如果创建了在整个快速入门中使用的名为 Azure AD SAML Toolkit 1 的测试应用程序，则可以考虑立即删除它以清理租户。 有关详细信息，请参阅[删除应用程序](delete-application-portal.md)。

## <a name="next-steps"></a>后续步骤

了解如何删除企业应用程序。
> [!div class="nextstepaction"]
> [删除应用程序](add-application-portal.md)
