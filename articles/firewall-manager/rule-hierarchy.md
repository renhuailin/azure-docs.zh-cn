---
title: 使用 Azure 防火墙策略定义规则层次结构
description: 了解如何使用 Azure 防火墙策略定义规则层次结构并强制实施合规性。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 1ba683e3d616f52854f1055dab9b9fe2d389116a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92331730"
---
# <a name="use-azure-firewall-policy-to-define-a-rule-hierarchy"></a>使用 Azure 防火墙策略定义规则层次结构

安全管理员需要管理防火墙，并确保跨本地部署和云部署的合规性。 一个关键因素是使应用程序团队能够灵活地实现 CI/CD 管道，从而以自动方式创建防火墙规则。

Azure 防火墙策略可用于定义规则层次结构并强制实施合规性：

- 提供一个层次结构，使中心基本策略覆盖子应用程序团队策略。 基本策略具有更高的优先级，并在子策略之前运行。
- 使用 Azure 自定义角色定义防止意外删除基本策略，并提供对订阅或资源组内规则集合组的选择性访问。 

## <a name="solution-overview"></a>解决方案概述

本示例的概要步骤如下：

1. 在安全团队资源组中创建基本防火墙策略。 
3. 在基本策略中定义特定于 IT 安全性的规则。 这会添加一组通用规则，以允许/拒绝流量。
4. 创建继承基本策略的应用程序团队策略。 
5. 在策略中定义特定于应用程序团队的规则。 还可以从预先存在的防火墙迁移规则。
6. 创建 Azure Active Directory 自定义角色以提供对规则集合组的精细访问，并在防火墙策略范围内添加角色。 在以下示例中，销售团队成员可以编辑销售团队防火墙策略的规则集合组。 这同样适用于数据库团队和工程团队。
7. 将策略关联到相应的防火墙。 Azure 防火墙只能有一个分配的策略。 这要求每个应用程序团队都有自己的防火墙。



:::image type="content" source="media/rule-hierarchy/contoso-teams.png" alt-text="团队和要求" border="false":::

### <a name="create-the-firewall-policies"></a>创建防火墙策略

- 基本防火墙策略。

为每个应用程序团队创建策略：

- 销售防火墙策略。 销售防火墙策略继承基本防火墙策略。
- 数据库防火墙策略。 数据库防火墙策略继承基本防火墙策略。
- 工程防火墙策略。 工程防火墙策略也继承基本防火墙策略。

:::image type="content" source="media/rule-hierarchy/policy-hierarchy.png" alt-text="策略层次结构" border="false":::

### <a name="create-custom-roles-to-access-the-rule-collection-groups"></a>创建自定义角色以访问规则集合组 

为每个应用程序团队定义自定义角色。 角色定义操作和范围。 应用程序团队可编辑其各自应用程序的规则集合组。

使用以下概要过程定义自定义角色：

1. 获取订阅：

   `Select-AzSubscription -SubscriptionId xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`
2. 运行以下命令：

   `Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize`
3. 使用 Get-AzRoleDefinition 命令以 JSON 格式输出 Reader 角色。 

   `Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json`
4. 在编辑器中打开 ReaderSupportRole.json 文件。

   下面显示了 JSON 输出。 有关不同属性的信息，请参阅  [Azure 自定义角色](../role-based-access-control/custom-roles.md)。

```json
   { 
     "Name": "Reader", 
     "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7", 
     "IsCustom": false, 
     "Description": "Lets you view everything, but not make any changes.", 
     "Actions": [ 
      "*/read" 
     ], 
     "NotActions": [], 
     "DataActions": [], 
     "NotDataActions": [], 
     "AssignableScopes": [ 
       "/" 
     ] 
   } 
```
5. 编辑 JSON 文件，以将 

   `*/read", "Microsoft.Network/*/read", "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write` 

   操作添加到 Actions ****   属性。 请确保在读取操作后包括一个逗号。 此操作允许用户创建和更新规则集合组。
6. 在 AssignableScopes **** 中，采用以下格式添加订阅 ID： 

   `/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`

   必须添加显式的订阅 ID，否则将不允许将角色导入到订阅中。
7. 删除 Id  ****  属性行，并将 IsCustom  ****  属性更改为 true。
8. 将 Name ****   和 Description ****   属性更改为“AZFM 规则集合组作者”和“具有此角色的用户可以编辑防火墙策略规则集合组”

JSON 文件应类似于以下示例：

```
{ 

    "Name":  "AZFM Rule Collection Group Author", 
    "IsCustom":  true, 
    "Description":  "Users in this role can edit Firewall Policy rule collection groups", 
    "Actions":  [ 
                    "*/read", 
                    "Microsoft.Network/*/read", 
                     "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write" 
                ], 
    "NotActions":  [ 
                   ], 
    "DataActions":  [ 
                    ], 
    "NotDataActions":  [ 
                       ], 
    "AssignableScopes":  [ 
                             "/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx"] 
} 
```
9. 若要创建新的自定义角色，请使用 New-AzRoleDefinition 命令，并指定 JSON 角色定义文件。 

   `New-AzRoleDefinition -InputFile "C:\CustomRoles\RuleCollectionGroupRole.json`

### <a name="list-custom-roles"></a>列出自定义角色

若要列出所有自定义角色，可以使用 Get-AzRoleDefinition 命令：

   `Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom`

还可以在 Azure 门户中查看自定义角色。 转到你的订阅，选择“访问控制(IAM)”、“角色” 。

:::image type="content" source="media/rule-hierarchy/sales-app-policy.png" alt-text="SalesAppPolicy":::

:::image type="content" source="media/rule-hierarchy/sales-app-policy-read.png" alt-text="SalesAppPolicy 读取权限":::

有关详细信息，请参阅[教程：使用 Azure PowerShell 创建 Azure 自定义角色](../role-based-access-control/tutorial-custom-role-powershell.md)。

### <a name="add-users-to-the-custom-role"></a>将用户添加到自定义角色

在门户中，可以将用户添加到“AZFM 规则集合组作者”角色，并提供对防火墙策略的访问权限。

1. 从门户中选择应用程序团队防火墙策略（例如，SalesAppPolicy）。
2. 选择“访问控制”。
3. 选择“添加角色分配”。
4. 将用户/用户组（例如，销售团队）添加到该角色。

对其他防火墙策略重复此过程。

### <a name="summary"></a>总结

具有自定义角色的防火墙策略现在提供对防火墙策略规则集合组的选择性访问。

用户无权执行以下操作：
- 删除 Azure 防火墙或防火墙策略。
- 更新防火墙策略层次结构、DNS 设置或威胁情报。
- 如果他们不是“AZFM 规则集合组作者”组的成员，则无权更新防火墙策略。

安全管理员可以使用基本策略强制实施防护措施，并根据企业要求阻止某些类型的流量（例如 ICMP）。 

## <a name="next-steps"></a>后续步骤

详细了解 [Azure 防火墙策略](policy-overview.md)。

