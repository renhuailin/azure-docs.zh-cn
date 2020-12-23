---
title: 在管理单元中添加、删除和列出组 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 的管理单元中管理组及其角色权限。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 092604429cf5a527d7ee62b412e879ad9991eace
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394741"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>在 Azure Active Directory 中的管理单元中添加和管理组

在 Azure Active Directory (Azure AD) 中，可以将组添加到管理单元，以便控制更精细的管理作用域。

若要准备使用 PowerShell 和 Microsoft Graph 管理单元管理，请参阅 [开始](admin-units-manage.md#get-started)使用。

## <a name="add-groups-to-an-administrative-unit"></a>向管理单元添加组

你可以使用 Azure 门户、PowerShell 或 Microsoft Graph 将组添加到管理单元。

### <a name="use-the-azure-portal"></a>使用 Azure 门户

只能将单个组分配给管理单元。 没有用于分配组作为大容量操作的选项。 在 Azure 门户中，可以通过以下两种方式之一将组分配到管理单元：

* 从 " **组** " 窗格：

  1. 在 Azure 门户中，请参阅 **Azure AD** 。
  1. 选择 " **组** "，然后选择要分配给管理单元的组。 
  1. 在左侧窗格中，选择 " **管理单位** " 以显示组分配到的管理单元的列表。 

     !["管理单元" 窗格上的 "分配到管理单元" 链接的屏幕截图。](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. 选择 " **分配到管理单元** "。
  1. 在右侧窗格中，选择 "管理单元"。

* 从 " **管理单元** " "  >  **所有组** " 窗格：

  1. 在 Azure 门户中，请参阅 **Azure AD** 。
  
  1. 在左侧窗格中，选择 " **管理单元** "，然后选择 " **所有组** "。 
     已分配到管理单元的任何组都将显示在右侧窗格中。 

  1. 在 " **组** " 窗格中，选择 " **添加** "。
    右窗格列出了 Azure AD 组织中的所有可用组。 

     ![用于向管理单元添加组的 "添加" 按钮的屏幕截图。](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. 选择要分配到管理单元的一个或多个组，然后选择 " **选择** " 按钮。

### <a name="use-powershell"></a>使用 PowerShell

在以下示例中，使用 `Add-AzureADMSAdministrativeUnitMember` cmdlet 将组添加到管理单元。 要添加的管理单元的对象 ID 和要添加的组的对象 ID 作为参数。 根据特定环境的需要，更改突出显示的部分。


```powershell
$administrative unitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

运行以下命令：

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref

Request body
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{id}"
}
```

示例：

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>查看管理单元中的组列表

### <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 在 Azure 门户中，请参阅 **Azure AD** 。

1. 在左侧窗格中，选择 " **管理单元** "，然后选择要查看其组的管理单元。 默认情况下，在左窗格中选择 " **所有用户** "。 

1. 在左侧窗格中，选择 " **组** "。 右窗格显示属于所选管理单元的组的列表。

   ![显示管理单元中的组列表的 "组" 窗格的屏幕截图。](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>使用 PowerShell

若要显示管理单元的所有成员的列表，请运行以下命令： 

```powershell
$administrative unitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId
```

若要显示属于管理单元成员的所有组，请使用以下代码片段：

```http
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

运行以下命令：

```http
HTTP request
GET /directory/administrativeUnits/{Admin id}/members/$/microsoft.graph.group
Request body
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>查看组的管理单元列表

### <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 在 Azure 门户中，请参阅 **Azure AD** 。

1. 在左侧窗格中，选择 " **组** " 以显示组的列表。

1. 选择一个组可打开该组的配置文件。 

1. 在左侧窗格中，选择 " **管理单元** " 以列出该组所属的所有管理单元。

   !["管理单元" 窗格的屏幕截图，其中显示了组分配到的管理单元。](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>使用 PowerShell

运行以下命令：

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

运行以下命令：

```http
https://graph.microsoft.com/v1.0/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>从管理单元删除组

### <a name="use-the-azure-portal"></a>使用 Azure 门户

可以通过以下两种方式之一从 Azure 门户中的管理单元中删除组：

- 从组中删除它概述：

  1. 在 Azure 门户中，请参阅 **Azure AD** 。
  1. 在左侧窗格中，选择 " **组** "，然后打开要从管理单元中删除的组的配置文件。
  1. 在左侧窗格中，选择 " **管理单位** " 以列出分配给组的所有管理单元。 
  1. 选择要从中删除组的管理单元，然后选择“从管理单元中删除”。

     !["管理单元" 窗格的屏幕截图，显示分配给所选管理单元的组的列表。](./media/admin-units-add-manage-groups/group-au-remove.png)

- 从管理单元中删除它：

  1. 在 Azure 门户中，请参阅 **Azure AD** 。
  1. 在左侧窗格中，选择 " **管理单元** "，然后选择组分配到的管理单元。
  1. 在左侧窗格中，选择 " **组** " 以列出分配给该管理单元的所有组。
  1. 选择要删除的组，然后选择 " **删除组** "。

    !["组" 窗格的屏幕截图，显示管理单元中的组列表。](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>使用 PowerShell

运行以下命令：

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

运行以下命令：

```http
https://graph.microsoft.com/v1.0/directory/AdministrativeUnits/<adminunit-id>/members/<group-id>/$ref
```

## <a name="next-steps"></a>后续步骤

- [向管理单元分配角色](admin-units-assign-roles.md)
- [对管理单元中的用户的管理](admin-units-add-manage-users.md)
