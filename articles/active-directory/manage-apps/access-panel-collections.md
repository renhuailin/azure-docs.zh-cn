---
title: 在 Azure Active Directory 中创建“我的应用”门户的集合 |Microsoft Docs
description: 使用“我的应用”集合自定义“我的应用”页，以便为最终用户提供更简单的“我的应用”体验。 以单独的选项卡的方式将应用程序组织到组中。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: davidmu
ms.reviewer: lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: 454f1d5513e7cee0535251ac6f6b02fd3b699643
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771398"
---
# <a name="create-collections-on-the-my-apps-portal"></a>在“我的应用”门户中创建集合

用户可以使用“我的应用”门户来查看和启动他们有权访问的基于云的应用程序。 默认情况下，用户可以访问的所有应用程序都列在一个页面上。 为了更好地为用户组织此页面，如果你有 Azure AD Premium P1 或 P2 许可证，就可以设置集合。 使用集合，你可以将相关的应用程序分组（例如，按作业角色、任务或项目），并在单独的选项卡上显示它们。集合实质上是对用户可以访问的应用程序应用筛选器，因此用户只会看到集合中已分配给他们的那些应用程序。

> [!NOTE]
> 本文介绍管理员可以如何启用和创建集合。 有关如何使用“我的应用”门户和集合的最终用户的信息，请参阅[访问和使用集合](../user-help/my-applications-portal-workspaces.md)。

## <a name="enable-the-latest-my-apps-features"></a>启用最新的“我的应用”功能

1. 打开 [Azure 门户](https://portal.azure.com/)，并以用户管理员或全局管理员身份登录。

2. 转到“Azure Active Directory” > “用户设置”。

3. 在“用户功能预览”下，选择“管理用户功能预览设置”。

4. 在“用户可以使用‘我的应用’的预览功能”下，选择以下选项之一：
   * “已选择”- 为特定组启用功能。 使用“选择组”选项可以选择要为其启用功能的组。  
   * “全部”- 为所有用户启用功能。

> [!NOTE]
> 若要打开“我的应用”门户，用户可以使用链接 `https://myapps.microsoft.com` 或组织的自定义链接，例如 `https://myapps.microsoft.com/contoso.com`。 启用新的“我的应用”体验后，已更新的“我的应用”体验将显示在“我的应用”页的顶部，用户可以选择“试用”以查看新体验。 要停止使用新体验，可以在页面顶部的“退出新体验”横幅中选择“是” 。

## <a name="create-a-collection"></a>创建集合

若要创建集合，你必须具有 Azure AD Premium P1 或 P2 许可证。

1. 打开 [Azure 门户](https://portal.azure.com/)，并以具有 Azure AD Premium P1 或 P2 许可证的管理员身份登录。

2. 转到“Azure Active Directory” > “企业应用程序”。

3. 在“管理”下，选择“集合”。

4. 选择“新建集合”。 在“新建集合”页中，输入集合的“名称”（我们建议不要在名称中使用“集合”。 然后输入“说明”。

   ![“新建集合”页](media/acces-panel-collections/new-collection.png)

5. 选择“应用程序”选项卡。选择“+ 添加应用程序”，然后在“添加应用程序”页中，选择要添加到集合中的所有应用程序，或使用“搜索”框查找应用程序。

   ![将应用程序添加到集合](media/acces-panel-collections/add-applications.png)

6. 添加完应用程序后，选择“添加”。 此时会显示所选应用程序的列表。 可以使用向上键更改列表中应用程序的顺序。 若要向下移动应用程序或将其从集合中删除，请选择“更多”菜单(...)。

7. 选择“所有者”选项卡。选择“+ 添加用户和组”，然后在“添加用户和组”页中，选择要将所有权分配到的用户或组。 选择完用户和组后，选择“选择”。

9. 选择“用户和组”选项卡。选择“+ 添加用户和组”，然后在“添加用户和组”页中，选择要将集合分配到的用户或组。 或使用“搜索”框查找用户或组。 选择完用户和组后，选择“选择”。

   ![添加用户和组](media/acces-panel-collections/add-users-and-groups.png)

11. 选择“查看 + 创建”  。 此时会显示新集合的属性。

> [!NOTE]
> 管理集合通过 [Azure 门户](https://portal.azure.com)而不是从[我的应用门户](https://myapps.microsoft.com)进行管理。 例如，如果将用户或组分配为所有者，则它们只能通过 Azure 门户管理集合。

## <a name="view-audit-logs"></a>查看审核日志

审核日志会记录“我的应用”集合操作，包括集合创建最终用户操作。 以下事件是从“我的应用”生成的：

* 创建管理员集合
* 编辑管理员集合
* 删除管理员集合
* 添加自助服务应用程序（最终用户）
* 删除自助服务应用程序（最终用户）

可以通过在“活动”部分中选择“Azure Active Directory” > “企业应用程序” > “审核日志”来访问 [Azure 门户](https://portal.azure.com)中的审核日志。 对于“服务”，选择“我的应用”。

## <a name="get-support-for-my-account-pages"></a>获取“我的帐户”页支持

在“我的应用”页中，用户可以选择“我的帐户” > “查看我的帐户”打开其帐户设置。 在 Azure AD“我的帐户”页上，用户可以管理其安全信息、设备、密码等。 他们还可以访问其 Office 帐户设置。

如果需要针对 Azure AD 帐户页或 Office 帐户页上的问题提交支持请求，请按照以下步骤进行操作，以便正确路由请求：

* 对于 Azure AD“我的帐户”页中的问题，请从 Azure 门户内打开支持请求。 转到“Azure 门户” > “Azure Active Directory” > “新建支持请求”。  

* 对于 Office“我的帐户”页的问题，请从 Microsoft 365 管理中心内打开支持请求。 转到“ Microsoft 365 管理中心” > “支持”。

## <a name="next-steps"></a>后续步骤

[Azure Active Directory 中应用程序的最终用户体验](end-user-experiences.md)
