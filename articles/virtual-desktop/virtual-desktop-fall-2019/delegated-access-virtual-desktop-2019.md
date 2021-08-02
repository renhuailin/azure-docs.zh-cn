---
title: Azure 虚拟桌面（经典）中的委托访问 - Azure
description: 如何在 Azure 虚拟桌面（经典）部署上委托管理功能，包括示例。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9db8ad454ad38f24f32e05bf2f72d67ef7db1971
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111752038"
---
# <a name="delegated-access-in-azure-virtual-desktop-classic"></a>Azure 虚拟桌面（经典）中的委托访问

>[!IMPORTANT]
>此内容适用于 Azure 虚拟桌面（经典版），后者不支持 Azure 资源管理器 Azure 虚拟桌面对象。 若要尝试管理 Azure 资源管理器 Azure 虚拟桌面对象，请参阅[此文](../delegated-access-virtual-desktop.md)。

Azure 虚拟桌面有一个委托访问模型，可通过为特定用户分配角色来定义允许该用户拥有的访问量。 角色分配包含 3 个组成部分：安全主体、角色定义和范围。 Azure 虚拟桌面委托访问模型基于 Azure RBAC 模型。 若要详细了解特定的角色分配及其组成部分，请查看 [Azure 基于角色的访问控制概述](../../role-based-access-control/built-in-roles.md)。

对于角色分配的各个元素，Azure 虚拟桌面委托访问支持以下值：

* 安全主体
    * 用户
    * 服务主体
* 角色定义
    * 内置角色
* 范围
    * 租户组
    * 租户
    * 主机池
    * 应用组

## <a name="built-in-roles"></a>内置角色

Azure 虚拟桌面中的委托访问有多个内置角色定义，这些定义可分配到用户和服务主体。

* RDS 所有者可以管理所有内容，包括对资源的访问权限。
* RDS 参与者可以管理所有内容，但无法访问资源。
* RDS 读取者可以查看所有内容，但不能进行任何更改。
* RDS 操作员可以查看诊断活动。

## <a name="powershell-cmdlets-for-role-assignments"></a>用于角色分配的 PowerShell cmdlet

可以运行以下 cmdlet 来创建、查看和删除角色分配：

* Get-RdsRoleAssignment 显示角色分配列表。
* New-RdsRoleAssignment 创建新角色分配。
* Remove-RdsRoleAssignment 删除角色分配。

### <a name="accepted-parameters"></a>接受的参数

可以使用以下参数来修改三个基本的 cmdlet：

* AadTenantId：指定服务主体所属的 Azure Active Directory 租户 ID。
* AppGroupName：远程桌面应用组的名称。
* Diagnostics：指示诊断范围。 （必须与“Infrastructure”或“Tenant”参数配对 。）
* HostPoolName：远程桌面主机池的名称。
* Infrastructure：指示基础结构范围。
* RoleDefinitionName：分配到用户、组或应用的远程桌面服务基于角色的访问控制角色的名称。 （例如，远程桌面服务所有者、远程桌面服务读取者，等等。）
* ServerPrincipleName：Azure Active Directory 应用程序的名称。
* SignInName：用户的电子邮件地址或用户主体名称。
* TenantName：远程桌面租户的名称。

## <a name="next-steps"></a>后续步骤

有关每个角色可使用的 PowerShell cmdlet 的更完整的列表，请查看 [PowerShell 参考](/powershell/windows-virtual-desktop/overview)。

有关如何设置 Azure 虚拟桌面环境的指南，请参阅 [Azure 虚拟桌面环境](environment-setup-2019.md)。
