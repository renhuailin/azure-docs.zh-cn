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
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34a6a353593d733cf50325049cd88034fb16c9ca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723118"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>在 Azure Active Directory 中管理管理单元

若要在 Azure Active Directory (Azure AD) 中实现更精细的管理控制，可将用户分配到范围限定为一个或多个管理单元的 Azure AD 角色。


## <a name="prerequisites"></a>先决条件

- 每个管理单元管理员都有 Azure AD Premium P1 或 P2 许可证
- 管理单元成员有 Azure AD 免费许可证
- 特权角色管理员或全局管理员
- 使用 PowerShell 时需要 AzureAD 模块
- 将 Graph 浏览器用于 Microsoft Graph API 时需要管理员同意

有关详细信息，请参阅[使用 PowerShell 或 Graph 浏览器的先决条件](prerequisites.md)。

## <a name="add-an-administrative-unit"></a>添加管理单元

可以使用 Azure 门户或 PowerShell 添加管理单元。

### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “管理单元” 。

    ![Azure AD 中“管理单元”链接的屏幕截图。](./media/admin-units-manage/nav-to-admin-units.png)

1. 选择窗格上部的“添加”按钮，然后在“名称”框中输入管理单元的名称。 （可选）添加管理单元的说明。

    ![此屏幕截图显示了“添加”按钮和用于输入管理单元名称的“名称”框。](./media/admin-units-manage/add-new-admin-unit.png)

1. 选择蓝色的“添加”按钮以完成管理单元的操作。

### <a name="powershell"></a>PowerShell

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

可以根据需要修改用引号引起来的值。

### <a name="microsoft-graph-api"></a>Microsoft Graph API

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

### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)或 [Azure AD 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “管理单元” 。
 
1. 键入要删除的管理单元，然后选择“删除”。

1. 若要确认是否要删除管理单元，请选择“是”。 此时会删除管理单元。

    ![管理单元的“删除”按钮和确认窗口的屏幕截图。](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="powershell"></a>PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -Id $adminUnitObj.Id
```

可以根据特定环境的需要修改用引号引起来的值。

### <a name="microsoft-graph-api"></a>Microsoft Graph API

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
