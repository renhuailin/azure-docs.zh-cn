---
title: 添加和删除管理单元 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中使用管理单元来限制角色权限的范围。
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0706fad1e5340625c32eab691ac3e4d58eeafc9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012085"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>在 Azure Active Directory 中管理管理单元

若要在 Azure Active Directory (Azure AD) 中实现更精细的管理控制，可将用户分配到范围限定为一个或多个管理单元的 Azure AD 角色。

## <a name="get-started"></a>入门

1. 若要通过 [Graph 资源管理器](https://aka.ms/ge)使用以下指令运行查询，请执行以下操作：

    a. 在 Azure 门户中，转到 Azure AD。 
    
    b. 在应用程序列表中，选择“Graph 浏览器”。
    
    c. 在“权限”窗格中，选择“为 Graph 浏览器授予管理员同意”。

    ![显示“为 Graph 浏览器授予管理员同意”链接的屏幕截图。](./media/admin-units-manage/select-graph-explorer.png)


1. 使用 [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/)。

## <a name="add-an-administrative-unit"></a>添加管理单元

可以使用 Azure 门户或 PowerShell 添加管理单元。

### <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 在 Azure 门户中，转到 Azure AD。 然后，在左侧窗格中，选择“管理单元”。

    ![Azure AD 中“管理单元”链接的屏幕截图。](./media/admin-units-manage/nav-to-admin-units.png)

1. 选择窗格上部的“添加”按钮，然后在“名称”框中输入管理单元的名称。 （可选）添加管理单元的说明。

    ![此屏幕截图显示了“添加”按钮和用于输入管理单元名称的“名称”框。](./media/admin-units-manage/add-new-admin-unit.png)

1. 选择蓝色的“添加”按钮以完成管理单元的操作。

### <a name="use-powershell"></a>使用 PowerShell

在尝试运行以下命令之前，请安装 [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/)：

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

可以根据需要修改用引号引起来的值。

### <a name="use-microsoft-graph"></a>使用 Microsoft Graph

请求

```http
POST /administrativeUnits
```

正文

```http
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>删除管理单元

在 Azure AD 中，可以删除不再需要作为管理角色的作用域的管理单元。

### <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 在 Azure 门户中，转到“Azure AD”，然后选择“管理单元” 。 
1. 键入要删除的管理单元，然后选择“删除”。 
1. 若要确认是否要删除管理单元，请选择“是”。 此时会删除管理单元。

![管理单元的“删除”按钮和确认窗口的屏幕截图。](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>使用 PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $adminUnitObj.ObjectId
```

可以根据特定环境的需要修改用引号引起来的值。

### <a name="use-the-graph-api"></a>使用 Graph API

请求

```http
DELETE /administrativeUnits/{admin-unit-id}
```

正文

```http
{}
```

## <a name="next-steps"></a>后续步骤

* [对管理单元中的用户的管理](admin-units-add-manage-users.md)
* [管理管理单元中的组](admin-units-add-manage-groups.md)
