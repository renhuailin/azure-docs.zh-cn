---
title: 重置 VPN 网关或连接以重新建立 IPsec 隧道
titleSuffix: Azure VPN Gateway
description: 重置连接或 VPN 网关以重建 IPsec 隧道。
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: adc2ffd63d73baaddce00324787df61061ea69dc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726627"
---
# <a name="reset-a-vpn-gateway-or-a-connection"></a>重置 VPN 网关或连接

如果丢失了一个或多个站点到站点 VPN 隧道上的跨界 VPN 连接，重置 Azure VPN 网关或网关连接会很有帮助。 在此情况下，本地 VPN 设备都在正常工作，但却无法与 Azure VPN 网关建立 IPsec 隧道。 本文帮助你重置 VPN 网关或网关连接。

## <a name="what-happens-during-a-reset"></a>重置期间会发生什么情况

### <a name="gateway-reset"></a>网关重置

VPN 网关由在活动备用配置中运行的两个 VM 实例组成。 重置网关时会重启网关，并对其重新应用跨界配置。 该网关将保持已有的公共 IP 地址。 这意味着不需要使用 Azure VPN 网关的新公共 IP 地址更新 VPN 路由器配置。

发出重置网关命令后，会立即重新启动 Azure VPN 网关的当前活动实例。 从活动实例（正在重新启动）故障转移到备用实例期间会有一个短暂的时间间隔。 该时间间隔应不超过 1 分钟。

如果在首次重新启动后未恢复连接，再次发出同一命令以重新启动第二个 VM 实例（新活动网关）。 如果连续请求两次重新启动，则重新启动这两个 VM 实例（活动和备用）的时间可能会略长一些。 这种情况会导致 VPN 连接出现较长的时间间隔，VM 需要最多 30 到 45 分钟才能完成重新启动。

在两次重新启动之后，如果仍然存在跨界连接问题，请从 Azure 门户提出支持请求。

### <a name="connection-reset"></a>连接重置

当你选择重置连接时，网关不会重新启动。 仅重置并还原所选的连接。

## <a name="reset-a-connection"></a>重置连接

您可以使用 Azure 门户轻松地重置连接。

1. 导航到要重置的 **连接** 。 可以通过以下方式查找连接资源：在 **所有资源** 中查找该连接资源，或导航到 **"网关名称"-> 连接-> "连接名称"**
1. 在 " **连接** " 页上，从左侧菜单中选择 " **重置** "。
1. 在 " **重置** " 页上，单击 " **重置** " 以重置连接。

   :::image type="content" source="./media/reset-gateway/reset-connection.png" alt-text="显示 Reset 的屏幕截图。":::

## <a name="reset-a-vpn-gateway"></a>重置 VPN 网关

在重置网关之前，请为每个 IPsec 站点到站点 (S2S) VPN 隧道验证下面列出的重要项目。 如果项目中存在任何不匹配，将导致 S2S VPN 隧道断开连接。 验证并更正本地网关和 Azure VPN 网关的配置能够避免网关上其他正在工作的连接出现不必要的重新启动和中断。

在重置网关之前，请检查以下各项：

* 在 Azure 和本地 VPN 策略中，为 Azure VPN 网关和本地 VPN 网关配置的 Internet IP 地址 (VIP) 正确。
* 在 Azure 和本地 VPN 网关上，预共享的密钥必须相同。
* 如果应用特定的 IPsec/IKE 配置，如加密、哈希算法和 PFS（完全向前保密），请确保 Azure 和本地 VPN 网关具有相同配置。

### <a name="azure-portal"></a><a name="portal"></a>Azure 门户

可使用 Azure 门户重置 Resource Manager VPN 网关。 如果想要重置经典网关，请参阅[经典部署模型](#resetclassic)的 PowerShell 步骤。

[!INCLUDE [portal steps](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="powershell"></a><a name="ps"></a>PowerShell

#### <a name="resource-manager-deployment-model"></a>Resource Manager 部署模型

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

用于重置网关的 cmdlet 是 Reset-AzVirtualNetworkGateway。 进行重置前，请确保拥有最新版本的 [PowerShell Az cmdlet](/powershell/module/az.network)。 以下示例将重置 TestRG1 资源组中名为 VNet1GW 的虚拟网络网关：

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

结果：

收到返回结果时，可假定网关重置成功。 但返回结果没有明确指出重置成功。 如要仔细查看历史记录，确定网关重置发生的确切时间，可在 [Azure 门户](https://portal.azure.com)中查看该信息。 在门户中，导航到“GatewayName”->“资源运行状况”。

#### <a name="classic-deployment-model"></a><a name="resetclassic"></a>经典部署模型

用于重置网关的 cmdlet 是 Reset-AzureVNetGateway。 用于服务管理的 Azure PowerShell cmdlet 必须在桌面上本地安装。 不能使用 Azure Cloud Shell。 进行重置前，请确保拥有最新版本的 [Service Management (SM) PowerShell cmdlet](/powershell/azure/servicemanagement/install-azure-ps#azure-service-management-cmdlets)。 使用此命令时，请确保使用的是虚拟网络的全名。 使用门户创建的经典 VNet 具有 PowerShell 所需的长名称。 可以使用“Get-AzureVNetConfig -ExportToFile C:\Myfoldername\NetworkConfig.xml”查看长名称。

以下示例重置名为“Group TestRG1 TestVNet1”的虚拟网络的网关（在门户中简单地显示为“TestVNet1”）：

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
```

结果：

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

### <a name="azure-cli"></a><a name="cli"></a>Azure CLI

若要重置网关，请使用 [az network vnet-gateway reset](/cli/azure/network/vnet-gateway) 命令。 以下示例将重置 TestRG5 资源组中名为 VNet5GW 的虚拟网络网关：

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

结果：

收到返回结果时，可假定网关重置成功。 但返回结果没有明确指出重置成功。 如要仔细查看历史记录，确定网关重置发生的确切时间，可在 [Azure 门户](https://portal.azure.com)中查看该信息。 在门户中，导航到“GatewayName”->“资源运行状况”。
