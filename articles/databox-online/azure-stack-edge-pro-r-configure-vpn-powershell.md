---
title: 使用 Azure PowerShell 在 Azure Stack Edge Pro R 设备上配置 VPN
description: 介绍如何使用 Azure PowerShell 脚本在 Azure Stack Edge Pro R 设备上配置 VPN，以创建 Azure 资源。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/23/2020
ms.author: alkohli
ms.openlocfilehash: 66edd4cad5b2f38696ef1df2030687bf4c7d9956
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102634169"
---
# <a name="configure-vpn-on-your-azure-stack-edge-pro-r-device-via-azure-powershell"></a>通过 Azure PowerShell 在 Azure Stack Edge Pro R 设备上配置 VPN

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

VPN 选项为从 Azure Stack Edge Pro R 设备到 Azure 的基于 TLS 的数据活动提供了另一层加密。 可以通过 Azure 门户或 Azure PowerShell 在 Azure Stack Edge Pro R 设备上配置 VPN。

本文介绍使用 Azure PowerShell 脚本在 Azure Stack Edge Pro R 设备上配置 VPN 以在云中创建配置所需的步骤。

## <a name="about-vpn-setup"></a>关于 VPN 设置

跨界 VPN 连接包含一个 Azure VPN 网关、一个本地 VPN 设备，以及将两者相连接的 IPsec S2S VPN 隧道。 典型的工作流包括以下步骤：

- 配置先决条件。
- 在 Azure 上设置必要的资源。
    - 创建并配置 Azure VPN 网关（虚拟网络网关）
    - 创建和配置代表本地网络和 VPN 设备的 Azure 本地网络网关。
    - 在 Azure VPN 网关与本地网络网关之间创建并配置 Azure VPN 连接。
    - 设置 Azure 防火墙并添加网络和应用规则。
    - 创建 Azure 路由表并添加路由。
- 在设备的本地 Web UI 中设置 VPN。 配置本地网络网关代表的本地 VPN 设备，以便与 Azure VPN 网关建立实际 S2S VPN 隧道。

以下部分提供了详细步骤。

## <a name="configure-prerequisites"></a>配置先决条件

1. 你应该有权访问按照[安装 Azure Stack Edge Pro R 设备](azure-stack-edge-pro-r-deploy-install.md)中的说明安装的 Azure Stack Edge Pro R 设备。 该设备将用作本地 VPN 设备，以与 Azure 建立 VPN 连接。 

2. VPN 设备应该具有静态公共 IP 地址（外部）。 此地址不应为 NAT。

3. 你应该有权访问在 Azure 中为 Azure Stack Edge 服务启用的有效 Azure 订阅。 使用此订阅在 Azure 中创建相应的资源，以管理 Azure Stack Edge Pro R 设备。  

4. 你有权访问用于访问 Azure Stack Edge Pro R 设备的 Windows 客户端。 你将使用此客户端以编程方式在云中创建配置。

    1. 要在 Windows 客户端上安装所需版本的 PowerShell，请运行以下命令：

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. 要连接到 Azure 帐户和订阅，请运行以下命令：

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        提供 Azure Stack Edge Pro R 设备正在使用的 Azure 订阅名称以配置 VPN。

    3. [下载所需脚本](https://aka.ms/ase-vpn-deployment)以在云中创建配置。 脚本将执行以下操作：
        
        - 创建 Azure 虚拟网络和以下子网：GatewaySubnet 和 AzureFirewallSubnet 。
        - 创建并配置 Azure VPN 网关。
        - 创建并配置 Azure 本地网络网关。
        - 在 Azure VPN 网关与本地网络网关之间创建并配置 Azure VPN 连接。
        - 创建 Azure 防火墙并添加网络规则和应用规则。
        - 创建 Azure 路由表并向其添加路由。


## <a name="use-the-script"></a>使用脚本

首先修改 `parameters.json` 文件以输入参数。 接下来，使用已修改的 json 文件运行脚本。

以下部分介绍了这些步骤。

### <a name="download-service-tags-file"></a>下载服务标记文件

在下载脚本的文件夹中，可能已有一个 `ServiceTags.json` 文件。 如果没有，可以下载服务标记文件。

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>修改参数文件

第一步是修改 `parameters.json` 文件，并保存所做的更改。 


对于创建的 Azure 资源，需要提供以下名称：

|参数名称  |说明  |
|---------|---------|
|virtualNetworks_vnet_name    | Azure 虚拟网络名称        |
|azureFirewalls_firewall_name     | Azure 防火墙名称        |
|routeTables_routetable_name     | Azure 路由表名称        |
|publicIPAddresses_VNGW_public_ip_name     | 虚拟网络网关的公共 IP 地址名称       |
|virtualNetworkGateways_VNGW_name    | Azure VPN 网关（虚拟网络网关）名称        |
|publicIPAddresses_firewall_public_ip_name     | Azure 防火墙的公共 IP 地址名称         |
|localNetworkGateways_LNGW_name    |Azure 本地网络网关名称          |
|connections_vngw_lngw_name    | Azure VPN 连接名称。 这是虚拟网络网关与本地网络网关之间的连接。       |
|location     |这是要在其中创建虚拟网络的区域。 选择与设备关联的区域相同的区域。         |

以下 IP 地址和地址空间与创建的 Azure 资源有关，包括虚拟网络和关联的子网（default、firewall、GatewaySubnet）。

|参数名称  |说明  |
|---------|---------|
|VnetIPv4AddressSpace    | 这是与虚拟网络关联的地址空间。       |
|DefaultSubnetIPv4AddressSpace    |这是与虚拟网络的 `Default` 子网关联的地址空间。         |
|FirewallSubnetIPv4AddressSpace    |这是与虚拟网络的 `Firewall` 子网关联的地址空间。          |
|GatewaySubnetIPv4AddressSpace    |这是与虚拟网络的 `GatewaySubnet` 关联的地址空间。          |
|GatewaySubnetIPv4bgpPeeringAddress    | 这是为 BGP 通信保留的 IP 地址，它基于与虚拟网络的 `GatewaySubnet` 关联的地址空间。          |

以下 IP 地址和地址空间与本地网络（部署 Azure Stack Edge Pro R 设备的位置）相关。

|参数名称  |说明  |
|---------|---------|
|CustomerNetworkAddressSpace    |  这是专用 IP 地址的地址空间。       |
|CustomerPublicNetworkAddressSpace    |  这是公共 IP 地址的地址空间。       |
|DbeIOTNetworkAddressSpace    |此 IP 地址由 IoT 服务保留。 请勿更改此参数。        |
|AzureVPNsharedKey    |此共享密钥在创建 Azure VPN 连接资源的过程中使用。 在配置本地 Web UI VPN 的过程中，此密钥也作为 VPN 共享机密提供。         |
|DBE-Gateway-ipaddress   | Azure Stack Edge Pro R 设备的公共 IP 地址。 这可能是未知的，你可以使用占位符 IP 地址运行脚本。 稍后可使用实际 IP 地址编辑本地网络网关。     |

#### <a name="caveats-to-keep-in-mind"></a>注意事项：

- 你没有 Azure Stack Edge Pro R 设备的 IP 地址。 可以使用占位符 IP 地址来创建资源，然后修改 Azure 本地网络网关，为设备分配实际的设备 IP 地址和本地网络的地址空间。
- 根据 IETF 在互联网号码分配机构 (IANA) 上的指示，请使用从 172.16.x.y 到 172.24.z.a 的任意子网。 我们保留 172.24 IPv4 地址范围用于 Azure 网络。

### <a name="run-the-script"></a>运行脚本

请按照以下步骤使用修改后的 `parameters.json` 并运行脚本以创建 Azure 资源。

1. 以管理员身份运行 PowerShell。

2. 切换到脚本所在的目录。

3. 运行该脚本。

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    下面显示了示例输出。

    `.\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "ServiceTags_Public_20191216.json"  -ResourceGroupName "devtestrg4" -AzureDeploymentName "dbetestdeployment20" -NetworkRuleCollectionName "testnrc20" -Priority 115 -AppRuleCollectionName "testarc20"`

    脚本运行大约需要 90 分钟。 脚本完成后，将在脚本所在的同一文件夹中生成部署日志。


## <a name="verify-the-azure-resources"></a>验证 Azure 资源

成功运行脚本后，验证是否在 Azure 中创建了所有资源。

接下来，将在设备的本地 Web UI 上配置 VPN。


## <a name="vpn-configuration-on-the-device"></a>设备上的 VPN 配置

[!INCLUDE [azure-stack-edge-gateway-configure-vpn-local-ui](../../includes/azure-stack-edge-gateway-configure-vpn-local-ui.md)]


## <a name="verify-vpn"></a>验证 VPN

[!INCLUDE [azure-stack-edge-gateway-verify-vpn](../../includes/azure-stack-edge-gateway-verify-vpn.md)]

## <a name="validate-data-transfer-through-vpn"></a>验证通过 VPN 进行的数据传输

要确认 VPN 能够正常工作，请将数据复制到 SMB 共享。 请按照在 Azure Stack Edge Pro R 设备上[添加共享](azure-stack-edge-gpu-manage-shares.md#add-a-share)中的步骤操作。 

1. 将文件（例如 \data\pictures\waterfall.jpg）复制到客户端系统上装载的 SMB 共享。 
2. 验证此文件是否显示在云中的存储帐户中。

验证数据是否通过 VPN 的方法：

1. 打开资源组中存在的连接资源。 

2. 检查数据输入和数据输出值。
 
3. 打开资源组中的虚拟网络网关。 查看隧道总流入量和隧道总流出量的图表 。
 
## <a name="debug-issues"></a>调试问题

要调试任何问题，请使用以下命令：

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

示例输出如下所示：


```azurepowershell
PS C:\Projects\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "aseprorvpnrg14_deployment" -ResourceGroupName "aseprorvpnrg14"


DeploymentName          : aseprorvpnrg14_deployment
ResourceGroupName       : aseprorvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 10/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     aseprorvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     aseprorvpnrg14_firewall
                          routeTables_routetable_name                  String                     aseprorvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     aseprorvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     aseprorvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     aseprorvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     aseprorvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     aseprorvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>后续步骤

[通过 Azure Stack Edge Pro R 设备上的本地 UI 配置 VPN](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)
