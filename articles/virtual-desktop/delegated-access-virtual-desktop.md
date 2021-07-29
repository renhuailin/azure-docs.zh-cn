---
title: Azure 虚拟桌面中的委托访问 - Azure
description: 如何在 Azure 虚拟桌面部署上委托管理功能，包括示例。
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 2367a2840e64956d3dfca41ce8ee5e31c9f354c7
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751984"
---
# <a name="delegated-access-in-azure-virtual-desktop"></a>Azure 虚拟桌面中的委托访问

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Azure 虚拟桌面对象的 Azure 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Azure 虚拟桌面（经典），请参阅[本文](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md)。

Azure 虚拟桌面有一个委托访问模型，可通过为特定用户分配角色来定义允许该用户拥有的访问量。 角色分配包含 3 个组成部分：安全主体、角色定义和范围。 Azure 虚拟桌面委托访问模型基于 Azure RBAC 模型。 若要详细了解特定的角色分配及其组成部分，请查看 [Azure 基于角色的访问控制概述](../role-based-access-control/built-in-roles.md)。

对于角色分配的各个元素，Azure 虚拟桌面委托访问支持以下值：

* 安全主体
    * 用户
    * 用户组
    * 服务主体
* 角色定义
    * 内置角色
    * 自定义角色
* 范围
    * 主机池
    * 应用组
    * 工作区

## <a name="powershell-cmdlets-for-role-assignments"></a>用于角色分配的 PowerShell cmdlet

在开始之前，请确保按照[设置 PowerShell 模块](powershell-module.md)中的说明设置 Azure 虚拟桌面 PowerShell 模块（如果尚未设置）。

在将应用组发布给用户或用户组时，Azure 虚拟桌面会使用 Azure 基于角色的访问控制 (Azure RBAC)。 会向用户或用户组分配“桌面虚拟化用户”角色，而范围是应用组。 此角色向用户提供对应用组的特殊数据访问权限。

若要将 Azure Active Directory 用户添加到应用组，请运行以下 cmdlet：

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

若要将 Azure Active Directory 用户组添加到应用组，请运行以下 cmdlet：

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="next-steps"></a>后续步骤

有关每个角色可使用的 PowerShell cmdlet 的更完整的列表，请查看 [PowerShell 参考](/powershell/windows-virtual-desktop/overview)。

有关 Azure RBAC 中支持的角色的完整列表，请查看 [azure 内置角色](../role-based-access-control/built-in-roles.md)。

有关如何设置 Azure 虚拟桌面环境的指南，请查看 [Azure 虚拟桌面环境](environment-setup.md)。
