---
title: Azure 虚拟桌面 PowerShell - Azure
description: 如何排查设置 Azure 虚拟桌面环境时 PowerShell 出现的问题。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: aae1f58eb9fc5e006254f6fdfd620c057e6d6fc3
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113762510"
---
# <a name="azure-virtual-desktop-powershell"></a>Azure 虚拟桌面 PowerShell

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Azure 虚拟桌面对象的 Azure 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Azure 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md)。

请参考本文来解决将 PowerShell 与 Azure 虚拟桌面配合使用时出现的错误和问题。 有关远程桌面服务 PowerShell 的详细信息，请参阅 [Azure 虚拟桌面 PowerShell](/powershell/windows-virtual-desktop/overview)。

## <a name="provide-feedback"></a>提供反馈

请访问 [Azure 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/bd-p/AzureVirtualDesktopForum)，与产品团队和活跃的社区成员共同探讨 Azure 虚拟桌面服务。

## <a name="powershell-commands-used-during-azure-virtual-desktop-setup"></a>在设置 Azure 虚拟桌面期间使用的 PowerShell 命令

本部分列出了在设置 Azure 虚拟桌面时通常使用的 PowerShell 命令，并提供使用这些命令时可能发生的问题的解决方法。

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>错误: New-AzRoleAssignment: 提供的信息未映射到 AD 对象 ID

```powershell
New-AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

原因：在绑定到 Azure 虚拟桌面环境的 Azure Active Directory 中找不到由 -SignInName 参数指定的用户。

解决方法：请确保符合以下要求。

- 该用户应已同步到 Azure Active Directory。
- 该用户不应绑定到企业到消费者 (B2C) 或企业到企业 (B2B) 商务应用。
- Azure 虚拟桌面环境应已绑定到正确的 Azure Active Directory。

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>错误: New-AzRoleAssignment:“具有该对象 ID 的客户端未获授权，无法在该范围执行操作(代码: AuthorizationFailed)”

原因 1：所用的帐户在订阅中没有“所有者”权限。

解决方法 1：需要由拥有“所有者”权限的用户执行角色分配。 或者，需要将相应用户分配到“用户访问管理员”角色，以将某个用户分配到应用程序组。

原因 2：所用的帐户拥有“所有者”权限，但不是环境中 Azure Active Directory 的一部分，或者无权查询该用户所在的 Azure Active Directory。

解决方法 2：需要由拥有“Active Directory”权限的用户执行角色分配。

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>错误: New-AzWvdHostPool -- 该位置不可用于该资源类型

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'.
```

原因：Azure 虚拟桌面支持选择主机池、应用程序组和工作区的位置，以将服务元数据存储在特定的位置。 你的选项仅限于可使用此功能的位置。 此错误表示该功能在所选位置不可用。

解决方法：错误消息中会列出支持的区域。 请改用某个支持的区域。

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>错误: New-AzWvdApplicationGroup 必须与主机池位于同一位置

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

原因：位置不匹配。 所有主机池、应用程序组和工作区都有一个用于存储服务元数据的位置。 所创建的相互关联的所有对象必须位于同一位置。 例如，如果主机池位于 `eastus`，则应用程序组也需要在 `eastus` 中创建。 如果创建一个工作区以将这些应用程序组注册到其中，则该工作区也需要位于 `eastus`。

解决方法：检索创建主机池的位置，然后将你要创建的应用程序组分配到该位置。

## <a name="next-steps"></a>后续步骤

- 如需大致了解如何排查 Azure 虚拟桌面问题和跟踪升级，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 若要排查设置 Azure 虚拟桌面环境和主机池时出现的问题，请参阅[环境和主机池创建](troubleshoot-set-up-issues.md)。
- 若要排查在 Azure 虚拟桌面中配置虚拟机 (VM) 时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要排查 Azure 虚拟桌面客户端连接问题，请参阅 [Azure 虚拟桌面服务连接](troubleshoot-service-connection.md)。
- 若要排查远程桌面客户端的问题，请参阅[排查远程桌面客户端问题](troubleshoot-client.md)
- 若要详细了解该服务，请参阅 [Azure 虚拟桌面环境](environment-setup.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](../azure-resource-manager/templates/deployment-history.md)。
