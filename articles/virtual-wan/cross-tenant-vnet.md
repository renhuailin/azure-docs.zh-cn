---
title: 将跨租户 VNet 连接到中心：PowerShell
titleSuffix: Azure Virtual WAN
description: 本文介绍了如何使用 PowerShell 将跨租户 VNet 连接到某个虚拟中心。
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: dc065e68d513850f9e006371861d087cccc6e155
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669846"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>将跨租户 VNet 连接到某个虚拟 WAN 中心

本文介绍了如何使用虚拟 WAN 将 VNet 连接到另一租户中的虚拟中心。 如果你的客户端工作负荷必须连接到同一网络，但它们位于不同的租户上，则此体系结构非常有用。 例如，如下图所示，可以将非 Contoso VNet（远程租户）连接到 Contoso 虚拟中心（父租户）。

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="设置路由配置" :::

在本文中，学习如何：

* 添加另一个租户作为你的 Azure 订阅上的参与者。
* 将跨租户 VNet 连接到某个虚拟中心。

此配置的步骤是混合使用 Azure 门户和 PowerShell 执行的。 但是，此功能本身仅通过 PowerShell 和 CLI 提供。

>[!NOTE]
> 请注意，跨租户虚拟网络连接只能通过 PowerShell 或 CLI 进行管理。 无法在 Azure 门户中管理跨租户虚拟网络连接。
> 
## <a name="before-you-begin"></a>开始之前

### <a name="prerequisites"></a>先决条件

若要使用本文中的步骤，你必须已在环境中设置以下配置：

* 父订阅中的虚拟 WAN 和虚拟中心。
* 在另一（远程）租户中的订阅中配置的虚拟网络。
* 请确保远程租户中的 VNet 地址空间不与已连接到父虚拟中心的任何其他 VNet 中的任何其他地址空间重叠。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>分配权限

要让通过虚拟中心管理父订阅的用户能够修改和访问远程租户中的虚拟网络，你需要为此用户分配“参与者”权限。 要为此用户分配“参与者”权限，可以在远程租户中的 VNET 的订阅中完成。

1. 为管理员添加“参与者”角色分配（负责管理虚拟 WAN 中心的管理员）。 可以使用 PowerShell 或 Azure 门户来分配此角色。 有关步骤，请参阅下面的 **添加或删除角色分配** 文章：

   * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [门户](../role-based-access-control/role-assignments-portal.md)

1. 接下来，将远程租户订阅和父租户订阅添加到 PowerShell 的当前会话。 运行以下命令。 如果你已登录到父租户，则只需针对远程租户运行此命令。

   ```azurepowershell-interactive
   Connect-AzAccount -SubscriptionId "[subscription ID]" -TenantId "[tenant ID]"
   ```

1. 验证角色分配是否成功，方法是使用父凭据登录 Azure PowerShell 并运行以下命令：

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. 如果权限已成功传播到父租户并已添加到会话中，则父租户和远程租户所拥有的订阅会显示在该命令的输出中。

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>将 VNet 连接到中心

在下面的步骤中，将虚拟网络链接到虚拟中心时，需在两个订阅的上下文之间进行切换。 根据你自己的环境替换示例值。

1. 运行以下命令，确保处于远程帐户的上下文中：

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[remote ID]"
   ```

1. 创建一个本地变量，用于存储要连接到中心的虚拟网络的元数据。

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[vnet name]" -ResourceGroupName "[resource group name]"
   ```

1. 切换回父帐户。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[parent ID]"
   ```

1. 将 VNet 连接到中心。

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[parent resource group name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $remote
   ```

1. 可以在 PowerShell 或 Azure 门户中查看新连接。

   * **PowerShell：** 如果连接已成功建立，则新建立的连接中的元数据会显示在 PowerShell 控制台中。
   * **Azure 门户：** 导航到虚拟中心 >“连接性”->“虚拟网络连接”。 你可以查看指向连接的指针。 若要查看实际资源，你需要适当的权限。
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>疑难解答

* 验证 $remote 中的元数据（来自前面的[节](#connect)）是否与 Azure 门户中的信息相匹配。
* 可以使用远程租户资源组的标识和访问管理设置或使用 Azure PowerShell 命令 (Get-AzSubscription) 来验证权限。
* 请确保将资源组的名称或任何其他特定于环境的变量用引号引起来（例如 "VirtualHub1" 或 "VirtualNetwork1"）。

## <a name="next-steps"></a>后续步骤

有关虚拟 WAN 的详细信息，请参阅：

* 虚拟 WAN [常见问题解答](virtual-wan-faq.md)
