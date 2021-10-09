---
title: 仅 Azure Active Directory 身份验证的 Azure Policy
description: 本文介绍如何在启用仅 Azure Active Directory (Azure AD) 身份验证的情况下，强制实施 Azure 策略创建 Azure SQL 数据库或 Azure SQL 托管实例
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/22/2021
ms.openlocfilehash: 672a46b39a726d5fd21a8c2d740008e169cf1e2c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128698995"
---
# <a name="azure-policy-for-azure-active-directory-only-authentication-with-azure-sql"></a>Azure SQL 中仅 Azure Active Directory 身份验证的 Azure Policy

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 本文中所述的“仅 Azure AD 身份验证”和相关的 Azure Policy 功能目前处于公共预览版。 

Azure Policy 可以强制创建 Azure SQL 数据库或 Azure SQL 托管实例，并在预配期间启用[仅 Azure AD 身份验证](authentication-azure-ad-only-authentication.md)。 使用此策略时，如果不是在启用仅 Azure AD 身份验证的情况下尝试[在 Azure 中创建逻辑服务器](logical-servers.md)或托管实例，那么任何尝试都会失败。

Azure Policy 可以应用于整个 Azure 订阅，也可以仅应用于资源组。

Azure Policy 中引入了两个新的内置策略：

- Azure SQL 数据库应已启用仅 Azure Active Directory 身份验证
- Azure SQL 托管实例应已启用仅 Azure Active Directory 身份验证

有关 Azure Policy 的详细信息，请参阅[什么是 Azure Policy？](/azure/governance/policy/overview)和 [Azure Policy 定义结构](/azure/governance/policy/concepts/definition-structure)。

## <a name="permissions"></a>权限

有关管理 Azure Policy 所需权限的概述，请参阅 [Azure Policy 中的 Azure RBAC 权限](/azure/governance/policy/overview#azure-rbac-permissions-in-azure-policy)。

### <a name="actions"></a>操作

如果要使用自定义角色来管理 Azure Policy，则需要执行以下[操作](/azure/role-based-access-control/role-definitions#actions)。

- */read
- Microsoft.Authorization/policyassignments/*
- Microsoft.Authorization/policydefinitions/*
- Microsoft.Authorization/policyexemptions/*
- Microsoft.Authorization/policysetdefinitions/*
- Microsoft.PolicyInsights/*

有关自定义角色的详细信息，请参阅 [Azure 自定义角色](/azure/role-based-access-control/custom-roles)。

## <a name="manage-azure-policy-for-azure-ad-only-authentication"></a>管理仅 Azure AD 身份验证的 Azure Policy

可以通过转到 [Azure 门户](https://portal.azure.com)并搜索“策略”服务来管理仅 Azure AD 身份验证策略。 在“定义”下，搜索“仅 Azure Active Directory 的身份验证”。

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/policy-azure-ad-only-authentication.png" alt-text="仅 Azure AD 身份验证的 Azure Policy 屏幕截图":::

有关如何为仅 Azure AD 身份验证添加 Azure Policy 的指南，请参阅[使用 Azure Policy 在 Azure SQL 中强制实施仅 Azure Active Directory 身份验证](authentication-azure-ad-only-authentication-policy-how-to.md)。

这些策略有三方面的影响：

- 审核 - 默认设置，将只在 Azure Policy 活动日志中捕获审核报告
- 拒绝 - 防止在未启用[仅 Azure AD 身份验证](authentication-azure-ad-only-authentication.md)的情况下创建逻辑服务器或托管实例
- 禁用 - 将禁用该策略，并且在未启用仅 Azure AD 身份验证的情况下不会限制用户创建逻辑服务器或托管实例

如果“仅 Azure AD 身份验证的 Azure Policy”设置为“拒绝”，则 Azure SQL 逻辑服务器或托管实例创建将失败。 此失败的详细信息将记录在资源组的“活动日志”中。

## <a name="policy-compliance"></a>策略符合性

可以查看“策略”服务下的“合规性”设置以查看合规性状态。 “符合性状态”将告知服务器或托管实例当前是否符合启用了仅 Azure AD 身份验证的要求。 

在未启用仅 Azure AD 身份验证的情况下，Azure Policy 可以阻止创建新的逻辑服务器或托管实例，但可以在服务器或托管实例创建后更改此功能。 如果用户在创建服务器或托管实例之后禁用了仅 Azure AD 身份验证，则在 Azure Policy 设置为“拒绝”时，符合性状态将为 `Non-compliant`。

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/check-compliance-policy-azure-ad-only-authentication.png" alt-text="仅 Azure AD 身份验证的 Azure Policy 合规性菜单屏幕截图":::

## <a name="limitations"></a>限制

- 目前，不能在已启用仅 Azure AD 身份验证的 Azure 门户中创建逻辑服务器或托管实例。 可以使用 Azure CLI、PowerShell、Rest API 或 ARM 模板创建启用了仅 Azure AD 身份验证的逻辑服务器或托管实例。 有关详细信息，请参阅[在 Azure SQL 中创建启用了仅 Azure AD 身份验证的服务器](authentication-azure-ad-only-authentication-create-server.md)。
- Azure Policy 在逻辑服务器或托管实例创建过程中强制实施仅 Azure AD 身份验证。 创建服务器后，具有特殊角色的授权 Azure AD 用户（例如，SQL 安全管理员）可以禁用仅 Azure AD 身份验证功能。 Azure Policy 允许这样做，但在这种情况下，服务器或托管实例将在符合性报告中列为 `Non-compliant`，报告将指示服务器或托管实例的名称。  
- 有关更多注解、已知问题和所需的权限，请参阅[仅 Azure AD 身份验证](authentication-azure-ad-only-authentication.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure Policy 强制 Azure SQL 实施仅 Azure Active Directory 身份验证](authentication-azure-ad-only-authentication-policy-how-to.md)
