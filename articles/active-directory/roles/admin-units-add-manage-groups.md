---
title: 在管理单元中添加、删除和列出组 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 的管理单元中管理组及其角色权限。
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: c824ee3a8365261fa6409bf79904cde1b409fc26
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734506"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>在 Azure Active Directory 的管理单元中添加和管理组

在 Azure Active Directory (Azure AD) 中，你可以向管理单元添加组，以获得更精细的管理控制范围。

## <a name="prerequisites"></a>先决条件

- 每个管理单元管理员都有 Azure AD Premium P1 或 P2 许可证
- 管理单元成员有 Azure AD 免费许可证
- 特权角色管理员或全局管理员
- 使用 PowerShell 时需要 AzureAD 模块
- 将 Graph 浏览器用于 Microsoft Graph API 时需要管理员同意

有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。

## <a name="add-groups-to-an-administrative-unit"></a>向管理单元添加组

可以使用 Azure 门户、PowerShell 或 Microsoft Graph 向管理单元添加组。

### <a name="azure-portal"></a>Azure 门户

只能将单个组分配给管理单元。 没有将组分配为批量操作的选项。 在 Azure 门户中，可以通过以下两种方式之一将组分配给管理单元：

* 从“组”窗格：

  1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

  1. 选择“Azure Active Directory” > “组”。

  1. 选择要分配给管理单元的组。

  1. 在左侧窗格中，选择“管理单元”以显示该组分配到的管理单元的列表。 

     ![“管理单元”窗格上“分配给管理单元”链接的屏幕截图。](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. 选择“分配给管理单元”。

  1. 在右侧窗格中，选择管理单元。

* 在“管理单元” > “组”窗格中 ：

  1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

  1. 选择“Azure Active Directory” > “管理单元” 。
  
  1. 选择要向其添加组的管理单元。
  
  1. 选择“组”。 已分配给管理单元的所有组显示在右侧窗格中。 

  1. 在“组”窗格中，选择“添加” 。
    右侧窗格列出了 Azure AD 组织中的所有可用组。 

     ![用于向管理单元添加组的“添加”按钮的屏幕截图。](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. 选择要分配给管理单元的一个或多个组，然后选择“选择”按钮。

### <a name="powershell"></a>PowerShell

在以下示例中，将使用 `Add-AzureADMSAdministrativeUnitMember` cmdlet 向管理单元添加组。 管理单元的对象 ID 和要添加的组的对象 ID 用作参数。 根据特定环境的需要更改突出显示的部分。


```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id -RefObjectId $GroupObj.ObjectId
```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

运行以下命令：

请求

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

正文

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{group-id}"
}
```

示例

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>查看管理单元中的组列表

### <a name="azure-portal"></a>Azure 门户

1. 在 Azure 门户中，转到“Azure AD”。

1. 在左侧窗格中，选择“管理单元”，然后选择要查看其组的管理单元。 默认情况下，左侧窗格中选择了“所有用户”。 

1. 在左侧窗格中，选择“组”。 右窗格显示作为所选管理单位成员的组的列表。

   ![“组”窗格的屏幕截图，其中显示了管理单元中的组列表。](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

若要显示管理单元的所有成员的列表，请运行以下命令： 

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id
```

若要显示属于管理单元成员的所有组，请使用以下代码片段：

```powershell
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

运行以下命令：

请求

```http
GET /directory/administrativeUnits/{admin-unit-id}/members/$/microsoft.graph.group
```

正文

```http
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>查看组的管理单元列表

### <a name="azure-portal"></a>Azure 门户

1. 在 Azure 门户中，转到“Azure AD”。

1. 在左侧窗格中，选择“组”以显示组列表。

1. 选择一个组可打开该组的配置文件。 

1. 在左侧窗格中，选择“管理单元”以列出该组所属的所有管理单元。

   ![“管理单元”窗格的屏幕截图，其中显示了组分配到的管理单元的列表。](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

运行以下命令：

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

运行以下命令：

```http
https://graph.microsoft.com/v1.0/groups/{group-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>从管理单元删除组

### <a name="azure-portal"></a>Azure 门户

可以通过以下两种方式之一从 Azure 门户中的管理单元中删除组：

- 从组概述中删除：

  1. 在 Azure 门户中，转到“Azure AD”。
  1. 在左侧窗格中，选择“组”，然后打开要从管理单元中删除的组的配置文件。
  1. 在左侧窗格中，选择“管理单元”以列出该组分配到的所有管理单元。 
  1. 选择要从中删除组的管理单元，然后选择“从管理单元中删除”。

     ![“管理单元”窗格的屏幕截图，其中显示了分配给所选管理单元的组的列表。](./media/admin-units-add-manage-groups/group-au-remove.png)

- 从管理单元中删除：

  1. 在 Azure 门户中，转到“Azure AD”。
  1. 在左侧窗格中，选择“管理单元”，然后选择该组分配到的管理单元。
  1. 在左侧窗格中，选择“组”以列出分配给该管理单元的所有组。
  1. 选择要删除的组，然后选择“删除组”。

    ![“组”窗格的屏幕截图，其中显示了管理单元中的组列表。](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

运行以下命令：

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitId -MemberId $memberGroupObjId
```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

运行以下命令：

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{group-id}/$ref
```

## <a name="next-steps"></a>后续步骤

- [向管理单元分配角色](admin-units-assign-roles.md)
- [对管理单元中的用户的管理](admin-units-add-manage-users.md)
