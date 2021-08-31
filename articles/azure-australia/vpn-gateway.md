---
title: Azure 澳大利亚中的 Azure VPN 网关
description: 在 Azure 澳大利亚实现 VPN 网关，以符合 ISM 要求并有效保护澳大利亚政府机构的安全
author: emilyre
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: yvettep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0221f28df5ddf53df0aad298cd6692c279b5fb75
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "117028970"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Azure 澳大利亚中的 Azure VPN 网关

任何公有云的一项关键服务是确保在云资源和服务与现有本地系统之间建立安全的连接。 在 Azure 中提供此功能的服务是 Azure VPN 网关。 本文概述了将 VPN 网关配置为符合澳大利亚信号局 (ASD) [信息安全手册 (ISM) 控制](https://acsc.gov.au/infosec/ism/)要求时要考虑的要点。

VPN 网关用于在 Azure 虚拟网络和另一个网络之间发送加密流量。 VPN 网关解决了三个方案：

- 站点到站点 (S2S)
- 点到站点 (P2S)
- 网络到网络

本文重点介绍 S2S VPN 网关。 示意图 1 显示 S2S VPN 网关配置示例。

![具有多站点连接的 VPN 网关](media/vpngateway-multisite-connection-diagram.png)

*示意图 1 - Azure S2S VPN 网关*

## <a name="key-design-considerations"></a>关键设计考虑因素

三个网络选项可用于将 Azure 连接到澳大利亚政府客户：

- ICON
- Azure ExpressRoute
- 公共 Internet

澳大利亚网络安全中心的 [Azure 使用者指南](https://servicetrust.microsoft.com/viewpage/Australia)建议将 VPN 网关（或同等“受保护”且经认证的第三方服务）与这三个网络选项配合使用。 这项建议旨在确保连接符合 ISM 对加密和完整性的控制。

### <a name="encryption-and-integrity"></a>加密和完整性

默认情况下，在建立连接期间，作为 IKE 握手的一部分，VPN 会协商加密和完整性算法及其参数。 在 IKE 握手期间，首选项的配置和顺序取决于 VPN 网关是发起方还是响应方。 此指定由 VPN 设备控制。 连接的最终配置由 VPN 设备的配置控制。 有关验证的 VPN 设备及其配置的详细信息，请参阅[关于 VPN 服务](../vpn-gateway/vpn-gateway-about-vpn-devices.md)。

VPN 网关可以通过为连接配置自定义 IPsec/IKE 策略来控制加密和完整性。

### <a name="resource-operations"></a>资源操作

VPN 网关通过公共 Internet 在 Azure 和非 Azure 环境之间建立连接。 ISM 提供与连接的显式授权相关的控制措施。 默认情况下，可以使用 VPN 网关在安全环境中创建未经授权的隧道。 组织使用 Azure 基于角色的访问控制 (Azure RBAC) 来控制谁可以创建和修改 VPN 网关及其连接，这一点至关重要。 Azure 没有内置角色来管理 VPN 网关，因此需要自定义角色。

严格控制对所有者、参与者和网络参与者角色的访问权限。 此外，建议使用 Azure Active Directory Privileged Identity Management 进行更精细的访问控制。

### <a name="high-availability"></a>高可用性

Azure VPN 网关可以建立多个连接，并支持同一个本地环境有多个本地 VPN 设备。 请见示意图 1。

Azure 中的虚拟网络可以包含多个 VPN 网关，这些网关可以采用独立的、主动-被动模式或主动-主动模式的配置进行部署。

建议采用[高可用性配置](../vpn-gateway/vpn-gateway-highlyavailable.md)部署所有 VPN 网关。 例如，两个本地 VPN 设备连接到主动-被动或主动-主动模式下的两个 VPN 网关。 请见示意图 2。

![VPN 网关冗余连接](media/dual-redundancy.png)

*示意图 2 - 主动-主动 VPN 网关和两个 VPN 设备*

### <a name="forced-tunneling"></a>强制隧道

强制隧道会将所有 Internet 绑定流量通过 VPN 网关重定向或强制传送回本地环境，以便进行检查和审核。 如果没有强制隧道，来自 Azure 中 VM 的 Internet 绑定流量会直接遍历 Azure 网络基础结构并传送到公共 Internet，因而无法选择对流量进行检查或审核。 当组织需要在环境中使用安全的 Internet 网关 (SIG) 时，强制隧道就变得尤为重要。

## <a name="detailed-configuration"></a>详细配置

### <a name="service-attributes"></a>服务属性

为澳大利亚政府配置的用于 S2S 连接的 VPN 网关必须具有以下属性：

|属性 | 强制值|
|--- | --- |
|gatewayType | “VPN”|
|

符合 ISM“受保护”控制措施所需的属性设置为：

|属性 | 强制值|
|--- |---|
|vpnType |“RouteBased”|
|vpnClientConfiguration/vpnClientProtocols | “IkeV2”|
|

Azure VPN 网关支持 IPsec 和 IKE 协议标准中的一系列加密算法。 默认策略集会借助各种第三方 VPN 设备最大化互操作性。 因此，在 IKE 握手期间可能会协商不符合要求的配置。 强烈建议在 VPN 网关的 vpnClientConfiguration 中应用[自定义 IPsec/IKE 策略](../vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell.md)参数，以确保连接符合 ISM 对本地环境与 Azure 连接的控制要求。 下表显示了关键属性。

|属性|建议值|强制值|
|---|---|---|
|saLifeTimeSeconds|<14,400 秒|>300 秒|
|saDataSizeKilobytes| |>1,024 KB|
|ipsecEncryption| |AES256-GCMAES256|
|ipsecIntegrity| |SHA256-GCMAES256|
|ikeEncryption| |AES256-GCMAES256|
|ikeIntegrity| |SHA256-GCMAES256|
|dhGroup|DHGroup14、DHGroup24、ECP256、ECP384|DHGroup2|
|pfsGroup|PFS2048、PFS24、ECP256、ECP384||
|

对于上表中的 dhGroup 和 pfsGroup，ECP256 和 ECP384 是首选设置，尽管也可以使用其他设置。

### <a name="related-services"></a>相关服务

设计和配置 Azure VPN 网关时，许多相关服务也必须存在且已配置。

|服务 | 必需的操作|
|--- | ---|
|虚拟网络 | VPN 网关会附加到虚拟网络。 在创建新的 VPN 网关之前，请先创建虚拟网络。|
|公共 IP 地址 | S2S VPN 网关需要公共 IP 地址，才能在本地 VPN 设备和 VPN 网关之间建立连接。 在创建 S2S VPN 网关之前，请先创建公共 IP 地址。|
|子网 | 为 VPN 网关创建虚拟网络的子网。|
|

## <a name="implementation-steps-using-powershell"></a>使用 PowerShell 的实现步骤

### <a name="azure-role-based-access-control"></a>Azure 基于角色的访问控制

1. 创建自定义角色。 例如 virtualNetworkGateway 参与者。 创建要分配给用户以允许其创建和修改 VPN 网关的角色。 自定义角色应允许执行以下操作：

   Microsoft.Network/virtualNetworkGateways/*  
   Microsoft.Network/connections/*  
   Microsoft.Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft.Network/publicIPAddresses/*  
   Microsoft.Network/publicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. 将自定义角色添加到用户以允许其创建并管理 VPN 网关和与本地环境的连接。

### <a name="create-a-vpn-gateway"></a>创建 VPN 网关

这些步骤假定你已创建虚拟网络。

1. 创建新的公共 IP 地址。
2. 创建 VPN 网关子网。
3. 创建 VPN 网关 IP 配置文件。
4. 创建 VPN 网关。
5. 为本地 VPN 设备创建本地网络网关。
6. 创建 IPsec 策略。 此步骤假定你使用的是自定义 IPsec/IKE 策略。
7. 使用 IPsec 策略在 VPN 网关和本地网络网关之间创建连接。

### <a name="enforce-tunneling"></a>强制执行隧道

如果需要使用强制隧道，请在创建 VPN 网关之前：

1. 创建路由表和路由规则。
2. 将路由表与相应子网相关联。

请在创建 VPN 网关之后：

- 在 VPN 网关上将 GatewayDefaultSite 设置为本地环境。

### <a name="example-powershell-script"></a>PowerShell 脚本示例

用于创建自定义 IPsec/IKE 策略的 PowerShell 脚本示例符合 ISM 对澳大利亚“受保护”安全分类的控制要求。

此示例假定虚拟网络、VPN 网关和本地网关已存在。

#### <a name="create-an-ipsecike-policy"></a>创建 IPsec/IKE 策略

下方示例脚本使用以下算法和参数创建 IPsec/IKE 策略：

- IKEv2：AES256、SHA256、DHGroup ECP256
- IPsec：AES256、SHA256、PFS ECP256、SA Lifetime 14,400 秒和 102,400,000 KB

```powershell
$custompolicy = New-AzIpsecPolicy `
                    -IkeEncryption AES256 `
                    -IkeIntegrity SHA256 `
                    -DhGroup ECP256 `
                    -IpsecEncryption AES256 `
                    -IpsecIntegrity SHA256 `
                    -PfsGroup ECP256 `
                    -SALifeTimeSeconds 14400 `
                    -SADataSizeKilobytes 102400000
```

#### <a name="create-a-s2s-vpn-connection-with-the-custom-ipsecike-policy"></a>创建采用 IPsec/IKE 策略的 S2S VPN 连接

```powershell
$vpngw = Get-AzVirtualNetworkGateway `
                    -Name "<yourVPNGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"
$localgw = Get-AzLocalNetworkGateway  `
                    -Name "<yourLocalGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"

New-AzVirtualNetworkGatewayConnection `
                    -Name "ConnectionName" `
                    -ResourceGroupName "<yourResourceGroupName>" `
                    -VirtualNetworkGateway1 $vpngw `
                    -LocalNetworkGateway2 $localgw `
                    -Location "Australia Central" `
                    -ConnectionType IPsec `
                    -IpsecPolicies $custompolicy `
                    -SharedKey "AzureA1b2C3"
```

## <a name="next-steps"></a>后续步骤

本文介绍了满足“信息安全手册”指定要求的特定 VPN 网关配置，这些配置可以确保澳大利亚政府“受保护”数据在传输过程中的安全。 有关如何配置 VPN 网关的步骤，请参阅：

- [Azure 虚拟网络网关概述](../vpn-gateway/index.yml)  
- [什么是 VPN 网关？](../vpn-gateway/vpn-gateway-about-vpngateways.md)  
- [使用 PowerShell 创建具有站点到站点 VPN 连接的虚拟网络](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)  
- [创建和管理 VPN 网关](../vpn-gateway/tutorial-create-gateway-portal.md)
