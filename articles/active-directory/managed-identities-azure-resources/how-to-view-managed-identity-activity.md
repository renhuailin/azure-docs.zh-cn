---
title: 查看托管标识的更新和登录活动
description: 有关查看托管标识执行的活动以及托管标识执行的身份验证的分步说明
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/26/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03ab5ed10dcb9127c5b62850228d22222b4225be
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814430"
---
# <a name="view-update-and-sign-in-activities-for-managed-identities"></a>查看托管标识的更新和登录活动

本文将介绍如何查看对托管标识执行的更新，以及托管标识进行的登录尝试。

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。
- 如果还没有 Azure 帐户，请[注册免费帐户](https://azure.microsoft.com/free/)。

## <a name="view-updates-made-to-user-assigned-managed-identities"></a>查看对用户分配的托管标识执行的更新

此过程演示如何查看对用户分配的托管标识执行的更新

1. 在 Azure 门户中，浏览到“活动日志”。

 ![在 Azure 门户中浏览到“活动日志”](./media/how-to-view-managed-identity-activity/browse-to-activity-log.png)

2. 选择“添加筛选器”搜索框，然后从列表中选择“操作”。

![开始构建搜索筛选器](./media/how-to-view-managed-identity-activity/start-adding-search-filter.png)

3. 在“操作”下拉列表中，输入以下操作名称：“删除用户分配的标识”和“写入 UserAssignedIdentities”。

![将操作添加到搜索筛选器](./media/how-to-view-managed-identity-activity/add-operations-to-search-filter.png)

4. 显示匹配操作时，选择一个操作以查看摘要。

![查看操作的摘要](./media/how-to-view-managed-identity-activity/view-summary-of-operation.png)

5. 选择“JSON”选项卡以查看有关操作的更多详细信息，然后滚动到“属性”节点，查看有关已修改的标识的信息。

![查看操作的详细信息](./media/how-to-view-managed-identity-activity/view-json-of-operation.png)

## <a name="view-role-assignments-added-and-removed-for-managed-identities"></a>查看为托管标识添加和删除的角色分配

 > [!NOTE] 
 > 你将需要按要查看其角色分配变更的托管标识的对象（主体）ID 进行搜索

1. 找到要查看其角色分配变更的托管标识。 如果是查找系统分配的托管标识，对象 ID 将显示在“标识”屏幕中的资源下。 如果是查找用户分配的标识，对象 ID 将显示在托管标识的“概述”页中。

用户分配的标识：

![获取用户分配的标识的对象 ID](./media/how-to-view-managed-identity-activity/get-object-id-of-user-assigned-identity.png)

系统分配的标识：

![获取系统分配的标识的对象 ID](./media/how-to-view-managed-identity-activity/get-object-id-of-system-assigned-identity.png)

2. 复制对象 ID。
3. 浏览到“活动日志”。

 ![在 Azure 门户中浏览到“活动日志”](./media/how-to-view-managed-identity-activity/browse-to-activity-log.png)

4. 选择“添加筛选器”搜索框，然后从列表中选择“操作”。

![开始构建搜索筛选器](./media/how-to-view-managed-identity-activity/start-adding-search-filter.png)

5. 在“操作”下拉列表中，输入以下操作名称：“创建角色分配”和“删除角色分配”。

![将角色分配操作添加到搜索筛选器](./media/how-to-view-managed-identity-activity/add-role-assignment-operations-to-search-filter.png)

6. 在搜索框中粘贴对象 ID；系统将自动筛选结果。

![按对象 ID 搜索](./media/how-to-view-managed-identity-activity/search-by-object-id.png)
 
7. 显示匹配操作时，选择一个操作以查看摘要。
 
![托管标识的角色分配的摘要](./media/how-to-view-managed-identity-activity/summary-of-role-assignment-for-msi.png)

## <a name="view-authentication-attempts-by-managed-identities"></a>查看托管标识的身份验证尝试

1. 浏览到“Azure Active Directory”。

![浏览到 Active Directory](./media/how-to-view-managed-identity-activity/browse-to-active-directory.png)

2.  从“监视”部分中选择“登录日志”。

![选择登录日志](./media/how-to-view-managed-identity-activity/sign-in-logs-menu-item.png)

3. 选择“托管标识登录”选项卡。

![托管标识登录](./media/how-to-view-managed-identity-activity/msi-sign-ins.png)

4. 登录事件现在将按托管标识进行筛选。

![托管标识登录事件](./media/how-to-view-managed-identity-activity/msi-sign-in-events.png) 

5.  若要在 Azure Active Directory 中查看标识的企业应用程序，请选择“托管标识 ID”列。
6.  若要查看 Azure 资源或用户分配的托管标识，请在 Azure 门户的搜索栏中按名称进行搜索。

## <a name="next-steps"></a>后续步骤

* [Azure 资源的托管标识](./overview.md)
* [Azure 活动日志](../../azure-monitor/essentials/activity-log.md)
* [Azure Active Directory 登录日志](../reports-monitoring/concept-sign-ins.md)