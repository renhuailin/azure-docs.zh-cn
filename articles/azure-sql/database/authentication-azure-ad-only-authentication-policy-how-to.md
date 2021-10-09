---
title: 使用 Azure Policy 强制实施仅限 Azure Active Directory 的身份验证
description: 本文将引导你使用 Azure Policy 来对 Azure SQL 数据库和 Azure SQL 托管实例强制实施“仅限 Azure Active Directory (Azure AD) 身份验证”
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/22/2021
ms.openlocfilehash: 24cb978a20bc3a6e2385ed37994389abb6d32a32
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699032"
---
# <a name="using-azure-policy-to-enforce-azure-active-directory-only-authentication-with-azure-sql"></a>使用 Azure Policy 强制 Azure SQL 实施仅限 Azure Active Directory 的身份验证

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 本文中所述的“仅限 Azure AD 身份验证”和相关的 Azure Policy 功能目前处于公共预览版。  

本文将指导你创建一个 Azure Policy，以在用户创建 Azure SQL 托管实例或 Azure SQL 数据库的[逻辑服务器](logical-servers.md)时强制实施仅限 Azure AD 的身份验证。 若要详细了解如何在创建资源期间进行仅限 Azure AD 的身份验证，请参阅[在 Azure SQL 中创建启用了仅限 Azure AD 的身份验证的服务器](authentication-azure-ad-only-authentication-create-server.md)。

在本文中，学习如何：

> [!div class="checklist"]
> - 创建一个 Azure Policy，以强制在启用[仅限 Azure AD 的身份验证](authentication-azure-ad-only-authentication.md)的情况下创建逻辑服务器或托管实例
> - 检查 Azure Policy 合规性

## <a name="prerequisite"></a>先决条件

- 具有管理 Azure 策略的权限。 有关详细信息，请参阅 [Azure Policy 中的 Azure RBAC 权限](/azure/governance/policy/overview#azure-rbac-permissions-in-azure-policy)。

## <a name="create-an-azure-policy"></a>创建 Azure Policy

首先，创建一个强制实施 SQL 数据库或托管实例预配的 Azure Policy，并启用仅限 Azure AD 的身份验证。

1. 转到 [Azure 门户](https://portal.azure.com)。
1. 搜索服务策略。
1. 在“创作”设置下，选择“定义”。
1. 在“搜索”框中，搜索“仅限 Microsoft Azure Active Directory 的身份验证”。

   有两个内置策略可用于强制仅限 Azure AD 的身份验证。 一个用于 SQL 数据库，另一个用于托管实例。

   - Azure SQL 数据库应已启用仅 Azure Active Directory 身份验证
   - Azure SQL 托管实例应已启用仅 Azure Active Directory 身份验证

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/policy-azure-ad-only-authentication.png" alt-text="仅限 Azure AD 的身份验证的 Azure Policy 屏幕截图":::

1. 选择你的服务的策略名称。 在此示例中，我们将使用 Azure SQL 数据库。 选择“Azure SQL 数据库应已启用仅 Azure Active Directory 身份验证”。
1. 在新菜单中选择“分配”。

   > [!NOTE]
   > 该菜单中的 JSON 脚本显示内置策略定义，可将其用作模板，以便为 SQL 数据库生成自定义 Azure Policy。 默认值设置为 `Audit`。

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/assign-policy-azure-ad-only-authentication.png" alt-text="分配仅限 Azure AD 的身份验证的 Azure Policy 屏幕截图":::

1. 在“基本信息”选项卡中，使用框侧面的选择器 ( ... ) 添加“范围”。  

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/selecting-scope-policy-azure-ad-only-authentication.png" alt-text="选择仅限 Azure AD 的身份验证的 Azure Policy 范围屏幕截图":::

1. 在“范围”窗格中，从下拉菜单中选择你的订阅，然后为此策略选择一个资源组。   完成后，使用“选择”按钮保存选定内容。

   > [!NOTE]
   > 如果未选择资源组，则策略将应用于整个订阅。

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/adding-scope-policy-azure-ad-only-authentication.png" alt-text="添加仅限 Azure AD 的身份验证的 Azure Policy 范围屏幕截图":::

1. 返回“基本信息”选项卡后，自定义分配名称并提供可选说明。   确保“策略实施”设置为“已启用” 。
1. 转到“参数”选项卡。取消选择“仅显示需要输入的参数”选项。 
1. 在“效果”下面，选择“拒绝”。  此设置将阻止创建未启用“仅限 Azure AD 的身份验证”的逻辑服务器。

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/deny-policy-azure-ad-only-authentication.png" alt-text="仅限 Azure AD 的身份验证的 Azure Policy 的“效果”参数的屏幕截图":::

1. 在“不合规消息”选项卡中，你可以自定义策略消息，该消息会在违反策略时显示。 消息将让用户了解在服务器创建过程中执行的策略。

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/non-compliance-message-policy-azure-ad-only-authentication.png" alt-text="仅限 Azure AD 身份验证的 Azure Policy 的不合规消息的屏幕截图":::

1. 选择“查看 + 创建”。 查看策略并选择“创建”按钮。

> [!NOTE]
> 新创建的策略可能需要一些时间才能被执行。

## <a name="check-policy-compliance"></a>检查策略符合性

可以检查“策略”服务下的“合规性”设置以查看合规性状态。 

搜索之前为策略指定的分配名称。

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/compliance-policy-azure-ad-only-authentication.png" alt-text="仅限 Azure AD 的身份验证的 Azure Policy 合规性屏幕截图":::

在启用仅限 Azure AD 的身份验证的情况下创建逻辑服务器后，策略报告将在“资源（按合规性状态）”视觉对象下面增加计数器的值。 你将能够看到合规或不合规的资源。

如果选择策略涵盖的资源组包含已创建的服务器，则策略报告将指示合规合和不合规的资源。

> [!NOTE]
> 更新合规性报告可能需要一些时间。 不会立即报告与资源创建或仅限 Azure AD 的身份验证设置相关的更改。    

## <a name="provision-a-server"></a>预配服务器

然后，可以尝试在分配了 Azure Policy 的资源组中预配逻辑服务器或托管实例。 如果在服务器创建过程中启用仅限 Azure AD 的身份验证，则预配将成功。 如果未启用仅限 Azure AD 的身份验证，则预配将失败。

有关详细信息，请参阅[在 Azure SQL 中创建启用了“仅限 Azure Active Directory 的身份验证”的服务器](authentication-azure-ad-only-authentication-create-server.md)。

## <a name="next-steps"></a>后续步骤

- [仅限 Azure AD 的身份验证的 Azure Policy](authentication-azure-ad-only-authentication-policy.md)概述
- [在 Azure SQL 中创建启用了仅限 Azure AD 的身份验证的服务器](authentication-azure-ad-only-authentication-create-server.md)
- [仅限 Azure AD 的身份验证](authentication-azure-ad-only-authentication.md)概述
