---
title: 删除 Azure 虚拟桌面主机池 - Azure
description: 如何在 Azure 虚拟桌面中删除主机池。
author: Heidilohr
ms.topic: how-to
ms.date: 07/23/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: f0ea0e89ef70983f5c74b37365305cfab3e984bc
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123098500"
---
# <a name="delete-a-host-pool"></a>删除主机池

在 Azure 虚拟桌面中创建的所有主机池都附加到会话主机和应用组。 若要删除主机池，需要删除与主机池关联的应用组和会话主机。 删除应用组相当简单，但删除会话主机就比较复杂了。 当你删除会话主机时，需要确保它没有任何活动的用户会话。 应注销会话主机上的所有用户会话，以防止用户丢失数据。

### <a name="portal"></a>[Portal](#tab/azure-portal)

若要在 Azure 门户中删除主机池，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 搜索并选择“Azure 虚拟桌面”。

3. 在页面左侧的菜单中，选择“主机池”，然后选择要删除的主机池的名称。

4. 在页面左侧的菜单中，选择“应用程序组”。

5. 选择要删除的主机池中的所有应用程序组，然后选择“删除”。

6. 在删除应用组后，转到页面左侧的菜单，然后选择“概述”。

7. 选择“删除” 。

8. 如果要删除的主机池中有会话主机，你会看到一条消息，要求你授权继续操作。 请选择“是”。

9. Azure 门户现在会删除所有会话主机，并删除主机池。 与会话主机相关的 VM 不会被删除，而是会保留在你的订阅中。

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

若要使用 PowerShell 删除主机池，需要先删除主机池中的所有应用组。 若要删除所有应用组，请运行下面的 PowerShell cmdlet：

```powershell
Remove-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
```

接下来，运行下面的 cmdlet 来删除主机池：

```powershell
Remove-AzWvdHostPool -Name <hostpoolname> -ResourceGroupName <resourcegroupname> -Force:$true
```

此 cmdlet 删除主机池的会话主机上的所有现有用户会话。 它还从主机池中注销会话主机。 任何相关的虚拟机 (VM) 仍存在于你的订阅中。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 删除主机池，需要先删除主机池中的所有应用组。 

若要删除所有应用组，请使用 [az desktopvirtualization applicationgroup delete](/cli/azure/desktopvirtualization/applicationgroup#az_desktopvirtualization_applicationgroup_delete) 命令：

```azurecli
az desktopvirtualization applicationgroup delete --name "MyApplicationGroup" --resource-group "MyResourceGroup"
```

接下来，使用 [az desktopvirtualization hostpool delete](/cli/azure/desktopvirtualization/hostpool#az_desktopvirtualization_hostpool_delete) 命令删除主机池：

```azurecli
az desktopvirtualization hostpool delete --force true --name "MyHostPool" --resource-group "MyResourceGroup"
```

此删除操作会删除主机池的会话主机上的所有现有用户会话。 它还从主机池中注销会话主机。 任何相关的虚拟机 (VM) 仍存在于你的订阅中。

---

## <a name="next-steps"></a>后续步骤

若要了解如何创建主机池，请查看下面这些文章：

- [使用 Azure 门户创建主机池](create-host-pools-azure-marketplace.md)
- [使用 PowerShell 创建主机池](create-host-pools-powershell.md)

若要了解如何配置主机池设置，请查看下面这些文章：

- [自定义主机池的远程桌面协议属性](customize-rdp-properties.md)
- [配置 Azure 虚拟桌面负载均衡方法](configure-host-pool-load-balancing.md)
- [配置个人桌面主机池分配类型](configure-host-pool-personal-desktop-assignment-type.md)
