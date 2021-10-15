---
title: 为 Azure-SSIS 集成运行时配置 Azure 虚拟网络
description: 了解如何为 Azure-SSIS 集成运行时配置 Azure 虚拟网络。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c6e66487211ed3928f393bb028ae9f4c06e3b0eb
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403113"
---
# <a name="virtual-network-configuration-for-an-azure-ssis-integration-runtime"></a>Azure-SSIS 集成运行时的虚拟网络配置

根据以下要求设置虚拟网络： 

- 确保 `Microsoft.Batch` 是承载 Azure-SSIS IR 的虚拟网络子网订阅下的已注册提供程序。 如果使用的是经典虚拟网络，请同时将 `MicrosoftAzureBatch` 加入到该虚拟网络的经典虚拟机参与者角色。 

- 请确保具有所需的权限。 有关详细信息，请参阅[设置权限](#perms)。

- 选择合适的子网，承载 Azure-SSIS IR。 有关详细信息，请参阅[选择子网](#subnet)。 

- 如果使用自己的 Azure-SSIS IR 公共 IP 地址，请参阅[选择静态公共 IP 地址](#publicIP)

- 如果在虚拟网络中使用你自己的域名系统 (DNS) 服务器，请参阅[设置 DNS 服务器](#dns_server)。 

- 如果在子网中使用网络安全组 (NSG)，请参阅[设置 NSG](#nsg)。 

- 如果使用 Azure ExpressRoute 或用户定义的路由 (UDR)，请参阅[使用 Azure ExpressRoute 或 UDR](#route)。 

- 确保虚拟网络的资源组（如果使用自己的公共 IP 地址，则为公共 IP 地址的资源组）可以创建和删除特定的 Azure 网络资源。 有关详细信息，请参阅[设置资源组](#resource-group)。 

- 如果按照 [Azure-SSIS IR 的自定义设置](./how-to-configure-azure-ssis-ir-custom-setup.md)中所述自定义 Azure-SSIS IR，则用于管理其节点的内部进程会使用从 172.16.0.0 到 172.31.255.255 的预定义范围内的专用 IP 地址。 因此，请确保虚拟网络或本地网络的专用 IP 地址范围不会与此范围冲突。

下图显示了 Azure-SSIS IR 所需的连接：

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png" alt-text="此图显示了 Azure-SSIS IR 所需的连接。":::

## <a name="set-up-permissions"></a><a name="perms"></a> 设置权限

创建 Azure-SSIS IR 的用户必须拥有以下权限：

- 如果要将 SSIS IR 加入 Azure 资源管理器虚拟网络，则有两种选择：

  - 使用内置的“网络参与者”角色。 此角色具有 _Microsoft.Network/\*_ 权限，具有比所需作用域更大的作用域。

  - 创建仅包括必需的 _Microsoft.Network/virtualNetworks/\*/join/action_ 权限的一个自定义角色。 如果你还想要使用自己的 Azure-SSIS IR 公共 IP 地址，同时将其加入 Azure 资源管理器虚拟网络，请在角色中包含 _Microsoft.Network/publicIPAddresses/*/join/action_ 权限。

- 如果要将 SSIS IR 加入经典虚拟网络，我们建议使用内置的“经典虚拟机参与者”角色。 否则，你必须定义包含加入虚拟网络权限的自定义角色。

## <a name="select-the-subnet"></a><a name="subnet"></a>选择子网

选择子网时： 

- 不要选择 GatewaySubnet 来部署 Azure-SSIS IR。 GatewaySubnet 专用于虚拟网络网关。 

- 确保选择的子网具有足够的可用地址空间以供 Azure-SSIS IR 使用。 将可用 IP 地址数保持为 IR 节点数的至少两倍。 Azure 会保留每个子网中的某些 IP 地址。 不能使用这些地址。 子网的第一个和最后一个 IP 地址仅为协议一致性而保留，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- 不要使用其他 Azure 服务（例如，SQL 数据库 SQL 托管实例、应用服务等）以独占方式占用的子网。 

## <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>选择静态公共 IP 地址

若要使用自己的 Azure-SSIS IR 公共 IP 地址，同时将其加入虚拟网络，请确保它们符合以下要求：

- 应仅提供尚未与其他 Azure 资源关联的两个未使用的 IP 地址。 当我们定期升级你的 Azure-SSIS IR 时，将使用一个额外的 IP 地址。 请注意，不能在活动的 Azure SSIS IR 之间共享一个公共 IP 地址。

- 这些 IP 地址应该是标准类型的静态 IP。 有关更多详细信息，请参阅[公共 IP 地址的 SKU](../virtual-network/public-ip-addresses.md#sku)。

- 它们应该都有 DNS 名称。 如果在创建 IP 地址时未提供 DNS 名称，可以在 Azure 门户中提供。

:::image type="content" source="media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png" alt-text="Azure-SSIS IR":::

- 这些 IP 地址和虚拟网络应位于同一区域中的同一订阅下。

## <a name="set-up-the-dns-server"></a><a name="dns_server"></a> 设置 DNS 服务器 
如果需要在 Azure-SSIS IR 加入的虚拟网络中使用自己的 DNS 服务器来解析专用主机名，请确保该服务器也可以解析 全局 Azure 主机名（例如，名为 `<your storage account>.blob.core.windows.net` 的 Azure 存储 Blob）。 

下面是建议的一种方法： 

-   配置自定义 DNS，以将请求转发到 Azure DNS。 可以在你自己的 DNS 服务器上将未解析的 DNS 记录转发到 Azure 递归解析程序 (168.63.129.16) 的 IP 地址。 

有关详细信息，请参阅[使用自己的 DNS 服务器的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。 

> [!NOTE]
> 请使用完全限定的域名 (FQDN) 作为专用主机名（例如，使用 `<your_private_server>.contoso.com` 而不是 `<your_private_server>`）。 或者，可以在 Azure-SSIS IR 上使用标准自定义设置将自己的 DNS 后缀（例如 `contoso.com`）自动追加到任何未限定的单标签域名后，将其转换为 FQDN，然后再在 DNS 查询中使用它。详见[标准自定义设置示例](./how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples)。 

## <a name="set-up-an-nsg"></a><a name="nsg"></a> 设置 NSG
如果需要为 Azure-SSIS IR 使用的子网实施 NSG，请允许入站和出站流量通过以下端口： 

-   **Azure-SSIS IR 的入站要求**

| 方向 | 传输协议 | 源 | 源端口范围 | 目标 | 目标端口范围 | 注释 |
|---|---|---|---|---|---|---|
| 入站 | TCP | BatchNodeManagement | * | VirtualNetwork | 29876、29877（如果将 IR 加入资源管理器虚拟网络） <br/><br/>10100、20100、30100（如果将 IR 加入经典虚拟网络）| 数据工厂服务使用这些端口来与虚拟网络中 Azure-SSIS IR 的节点通信。 <br/><br/> 无论是否创建子网级 NSG，数据工厂都始终会在附加到托管 Azure-SSIS IR 的虚拟机的网络接口卡 (NIC) 级别配置 NSG。 此 NIC 级别的 NSG 仅允许来自指定端口上的数据工厂 IP 地址的入站流量。 即使在子网级别为 Internet 流量打开这些端口，来自 IP 地址（非数据工厂 IP 地址）的流量也会在 NIC 级别被阻止。 |
| 入站 | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | （可选）仅当 Microsoft 支持人员在高级故障排除期间要求客户打开此端口时，才需要此规则。故障排除后可立即将其关闭。 **CorpNetSaw** 服务标记仅允许 Microsoft 企业网络中的安全访问工作站使用远程桌面。 无法在门户中选择此服务标记，只能通过 Azure PowerShell 或 Azure CLI 选择。 <br/><br/> 在 NIC 级别的 NSG 中，端口 3389 默认已打开，你可以在子网级 NSG 中控制端口 3389，同时，出于保护目的，Azure-SSIS IR 默认已在每个 IR 节点上的 Windows 防火墙规则中禁用 3389 出站端口。 |
||||||||

-   **Azure-SSIS IR 的出站要求**

| 方向 | 传输协议 | 源 | 源端口范围 | 目标 | 目标端口范围 | 注释 |
|---|---|---|---|---|---|---|
| 出站 | TCP | VirtualNetwork | * | AzureCloud | 443 | 虚拟网络中 Azure-SSIS IR 的节点使用此端口来访问 Azure 服务，例如 Azure 存储和 Azure 事件中心。 |
| 出站 | TCP | VirtualNetwork | * | Internet | 80 | （可选）虚拟网络中的 Azure-SSIS IR 节点使用此端口从 Internet 下载证书吊销列表。 如果阻止此流量，在启动 IR 时可能会出现性能下降，并且无法在证书吊销列表中检查证书的使用情况。 若要进一步将目标范围缩小为特定的 FQDN，请参阅 **使用 Azure ExpressRoute 或 UDR** 部分|
| 出站 | TCP | VirtualNetwork | * | Sql | 1433、11000-11999 | （可选）仅当虚拟网络中 Azure-SSIS IR 的节点访问服务器托管的 SSISDB 时，才需要此规则。 如果服务器连接策略设置为“代理”而不是“重定向”，则只需使用端口 1433。  <br/><br/> 此出站安全规则不适用于虚拟网络中 SQL 托管实例托管的 SSISDB 或配置了专用终结点的 SQL 数据库。 |
| 出站 | TCP | VirtualNetwork | * | VirtualNetwork | 1433、11000-11999 | （可选）仅当虚拟网络中 Azure-SSIS IR 的节点访问虚拟网络中由 SQL 托管实例托管的 SSISDB 或配置了专用终结点的 SQL 数据库时，才需要此规则。 如果服务器连接策略设置为“代理”而不是“重定向”，则只需使用端口 1433。  |
| 出站 | TCP | VirtualNetwork | * | 存储 | 445 | （可选）仅当你要执行 Azure 文件存储中存储的 SSIS 包时，才需要此规则。 |
||||||||

## <a name="use-azure-expressroute-or-udr"></a><a name="route"></a> 使用 Azure ExpressRoute 或 UDR
若要检查来自 Azure-SSIS IR 的出站流量，可以通过 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 强制隧道将 Azure-SSIS IR 发起的流量路由到本地防火墙设备（将 BGP 路由从 0.0.0.0/0 播发到虚拟网络），或者通过 [UDR](../virtual-network/virtual-networks-udr-overview.md) 将此流量路由到充当防火墙或 [Azure 防火墙](../firewall/index.yml)的网络虚拟设备 (NVA)。 

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png" alt-text="Azure-SSIS IR 的 NVA 方案":::

需要执行以下操作才能使整个方案正常工作
   -   不能通过防火墙设备路由 Azure Batch 管理服务与 Azure-SSIS IR 之间的入站流量。
   -   防火墙设备应允许 Azure-SSIS IR 所需的出站流量。

不能将 Azure Batch 管理服务与 Azure-SSIS IR 之间的入站流量路由到防火墙设备，否则流量会由于非对称路由问题而中断。 必须为入站流量定义路由，使流量能够以其传入时的相同方式做出回复。 可以定义特定的 UDR，在 Azure Batch 管理服务与下一跃点类型为“Internet”的 Azure-SSIS IR 之间路由流量。

例如，如果 Azure-SSIS IR 位于 `UK South`，并且你想要通过 Azure 防火墙检查出站流量，则需要先从[服务标记 IP 范围下载链接](https://www.microsoft.com/download/details.aspx?id=56519)或通过[服务标记发现 API](../virtual-network/service-tags-overview.md#service-tags-on-premises) 获取服务标记 `BatchNodeManagement.UKSouth` 的 IP 范围列表。 然后，将下一个跃点类型作为“Internet”应用相关 IP 范围路由的以下 UDR，以及将下一个跃点类型作为“虚拟设备”应用 0.0.0.0/0 路由。

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png" alt-text="Azure Batch UDR 设置":::

> [!NOTE]
> 此方法会产生额外的维护成本。 定期检查 IP 范围，并在 UDR 中添加新的 IP 范围，以免中断 Azure-SSIS IR。 建议每月检查 IP 范围，因为当新 IP 出现在服务标记中时，该 IP 需要再等一个月才能生效。 

若要简化 UDR 规则的设置，可以运行以下 PowerShell 脚本，为 Azure Batch 管理服务添加 UDR 规则：
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

要使防火墙设备允许出站流量，需要根据 NSG 出站规则中的相同要求，允许向以下端口发送出站流量。
-   目标为 Azure 云服务的端口 443。

    如果使用 Azure 防火墙，则可使用 AzureCloud 服务标记指定网络规则。 对于其他类型的防火墙，可以简单地将端口 443 的目标设置为“全部”，也可以根据 Azure 环境的类型允许以下 FQDN：

    | Azure 环境 | 终结点                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li><b>Azure 数据工厂（管理）</b><ul><li>\*.frontend.clouddatahub.net</li></ul></li><li><b>Azure 存储（管理）</b><ul><li>\*.blob.core.windows.net</li><li>\*.table.core.windows.net</li></ul></li><li><b>Azure 容器注册表（自定义设置）</b><ul><li>\*.azurecr.io</li></ul></li><li><b>事件中心（日志记录）</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Microsoft 日志记录服务（内部使用）</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure 数据工厂（管理）</b><ul><li>\*.frontend.datamovement.azure.us</li></ul></li><li><b>Azure 存储（管理）</b><ul><li>\*.blob.core.usgovcloudapi.net</li><li>\*.table.core.usgovcloudapi.net</li></ul></li><li><b>Azure 容器注册表（自定义设置）</b><ul><li>\*.azurecr.us</li></ul></li><li><b>事件中心（日志记录）</b><ul><li>\*.servicebus.usgovcloudapi.net</li></ul></li><li><b>Microsoft 日志记录服务（内部使用）</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure 中国世纪互联     | <ul><li><b>Azure 数据工厂（管理）</b><ul><li>\*.frontend.datamovement.azure.cn</li></ul></li><li><b>Azure 存储（管理）</b><ul><li>\*.blob.core.chinacloudapi.cn</li><li>\*.table.core.chinacloudapi.cn</li></ul></li><li><b>Azure 容器注册表（自定义设置）</b><ul><li>\*.azurecr.cn</li></ul></li><li><b>事件中心（日志记录）</b><ul><li>\*servicebus.chinacloudapi.cn</li></ul></li><li><b>Microsoft 日志记录服务（内部使用）</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    至于 Azure 存储、Azure 容器注册表和事件中心的 FQDN，还可以选择为虚拟网络启用以下服务终结点，使发往这些终结点的网络流量通过 Azure 主干网络而不是路由到防火墙设备：
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   目标为 CRL 下载站点的端口 80。

    应允许以下 FQDN，它们用作证书（用于 Azure-SSIS IR 管理目的）的 CRL（证书吊销列表）下载站点：
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    如果使用的证书具有不同的 CRL，我们建议同时包含这些证书。 可以阅读[证书吊销列表](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx)了解详细信息。

    如果禁止此流量，在启动 Azure-SSIS IR 时可能会出现性能下降，并且无法在证书吊销列表中检查证书的使用情况，从安全的立场讲，我们不建议将它禁止。

-   目标为 Azure SQL 数据库的端口 1433 和端口范围 11000-11999（仅当虚拟网络中 Azure-SSIS IR 的节点访问服务器托管的 SSISDB 时，才需要此规则）。

    如果使用 Azure 防火墙，可以使用 Azure SQL 服务标记指定网络规则，否则，可以在防火墙设备中允许将特定的 Azure SQL URL 用作目标。

-   目标为 Azure 存储的端口 445（仅当执行 Azure 文件存储中存储的 SSIS 包时，才需要此规则）。

    如果使用 Azure 防火墙，可以使用存储服务标记指定网络规则，否则，可以在防火墙设备中允许将特定的 Azure 文件共享 URL 用作目标。

> [!NOTE]
> 对于 Azure SQL 和存储，如果在子网中配置虚拟网络服务终结点，则同一区域中 Azure-SSIS IR 和 Azure SQL 与同一区域或配对区域中 Azure 存储之间的流量将直接路由到 Microsoft Azure 主干网络，而不会路由到防火墙设备。

如果你不需要检查 Azure-SSIS IR 出站流量的功能，则可以直接应用路由，以强制所有流量路由到下一跃点类型“Internet”：

-   在 Azure ExpressRoute 方案中，可以在承载 Azure-SSIS IR 的子网上应用下一跃点类型为“Internet”的 0.0.0.0/0 路由。 
-   在 NVA 方案中，可将承载 Azure-SSIS IR 的子网中应用的现有 0.0.0.0/0 路由的下一跃点类型从“虚拟设备”修改为“Internet”。 

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png" alt-text="添加路由":::

> [!NOTE]
> 指定下一跃点类型为“Internet”的路由并不意味着流量将通过 Internet 传送。 只要目标地址用于 Azure 的某个服务，Azure 就会将流量通过 Azure 的主干网络直接路由到该服务，而不是将流量路由到 Internet。

## <a name="set-up-the-resource-group"></a><a name="resource-group"></a> 设置资源组

Azure-SSIS IR 需要在与虚拟网络相同的资源组下创建某些网络资源。 这些资源包括：
- Azure 负载均衡器，名为 \<Guid>-azurebatch-cloudserviceloadbalancer。
- Azure 公共 IP 地址，名为 \<Guid>-azurebatch-cloudservicepublicip。
- 网络工作安全组，名为 \<Guid>-azurebatch-cloudservicenetworksecuritygroup。 

> [!NOTE]
> 现在，可为 Azure-SSIS IR 提供自己的静态公共 IP 地址。 在此方案中，我们只会在与静态公共 IP 地址（而不是虚拟网络）相同的资源组下创建 Azure 负载均衡器和网络安全组。

当 Azure-SSIS IR 启动时，将创建这些资源。 当 Azure-SSIS IR 停止时，将删除这些资源。 如果为 Azure-SSIS IR 提供你自己的静态公共 IP 地址，则当 Azure-SSIS IR 停止时，系统不会删除你自己的静态公共 IP 地址。 为了避免阻止 Azure-SSIS IR 停止，请不要在其他资源中重复使用这些网络资源。

确保虚拟网络/静态公共 IP 地址所属的资源组/订阅中没有任何资源锁。 如果配置只读/删除锁，则启动和停止 Azure-SSIS IR 将会失败，或者它会停止响应。

确保没有任何 Azure Policy 分配阻止在虚拟网络/静态公共 IP 地址所属的资源组/订阅下创建以下资源： 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

请确保订阅的资源配额满足上述三种网络资源。 具体而言，对于在虚拟网络中创建的每个 Azure-SSIS IR，需要为上述三个网络资源中的每个资源保留两个可用配额。 当我们定期升级你的 Azure-SSIS IR 时，将使用一个额外的配额。

## <a name="faq"></a><a name="faq"></a> 常见问题解答

- 如何保护用于入站连接的 Azure-SSIS IR 上公开的公共 IP 地址？ 是否可以删除公共 IP 地址？
 
  目前，当 Azure-SSIS IR 加入虚拟网络时，会自动创建一个公共 IP 地址。 我们确实有一个 NIC 级别的 NSG，它只允许 Azure Batch 管理服务入站连接到 Azure-SSIS IR。 你也可以指定子网级别的 NSG 进行入站保护。

  如果你不希望公开任何公共 IP 地址，请考虑[将自承载 IR 配置为 Azure-SSIS IR 的代理](./self-hosted-integration-runtime-proxy-ssis.md)，而不是将 Azure-SSIS IR 加入虚拟网络（如果这适用于你的方案）。
 
- 是否可以将 Azure-SSIS IR 的公共 IP 地址添加到数据源的防火墙允许列表？

  现在，可为 Azure-SSIS IR 提供自己的静态公共 IP 地址。 在这种情况下，可将 IP 地址添加到数据源的防火墙允许列表。 还可以根据自己的情况，考虑使用以下其他选项来保护从 Azure-SSIS IR 进行的数据访问：

  - 如果数据源位于本地，则在将虚拟网络连接到本地网络并将 Azure-SSIS IR 加入虚拟网络子网后，可以将该子网的专用 IP 地址范围添加到数据源的防火墙允许列表。
  - 如果数据源是支持虚拟网络服务终结点的 Azure 服务，则你可以在虚拟网络子网中配置一个虚拟网络服务终结点，并将 Azure-SSIS IR 加入该子网。 然后，可将包含该子网的虚拟网络规则添加到数据源的防火墙。
  - 如果数据源是非 Azure 云服务，则你可以使用 UDR 通过静态公共 IP 地址将来自 Azure-SSIS IR 的出站流量路由到 NVA/Azure 防火墙。 然后，可将 NVA/Azure 防火墙的静态公共 IP 地址添加到数据源的防火墙允许列表。
  - 如果上述选项都不能满足你的需求，请考虑[将自承载 IR 配置为 Azure-SSIS IR 的代理](./self-hosted-integration-runtime-proxy-ssis.md)。 然后，可将托管自承载 IR 的计算机的静态公共 IP 地址添加到数据源的防火墙允许列表。

- 如果我要为 Azure-SSIS IR 提供静态公共地址，为何需要提供两个？

  Azure-SSIS IR 将定期自动更新。 升级期间将创建新节点，而旧节点将被删除。 但是，为了避免停机，只有在新节点准备就绪之后才会删除旧节点。 因此，旧节点使用的第一个静态公共 IP 地址无法立即释放，我们需要第二个静态公共 IP 地址来创建新节点。

- 我已经为 Azure-SSIS IR 提供了自己的静态公共 IP 地址，但为何它仍然无法访问我的数据源？

  - 确认两个静态公共 IP 地址都已添加到数据源的防火墙允许列表。 每次升级 Azure-SSIS IR 时，其静态公共 IP 地址将在提供的两个地址之间切换。 如果只将其中一个地址添加到允许列表，则升级后，Azure-SSIS IR 的数据访问将会中断。
  - 如果数据源是 Azure 服务，请检查是否已为它配置虚拟网络服务终结点。 如果是这种情况，从 Azure-SSIS IR 到数据源的流量将切换为使用由 Azure 服务管理的专用 IP 地址，并且将你自己的静态公共 IP 地址添加到数据源的防火墙允许列表将不会生效。

## <a name="next-steps"></a>后续步骤
- [将 Azure-SSIS 集成运行时加入虚拟网络 - 概述](join-azure-ssis-integration-runtime-virtual-network.md)
- [使用 Azure 数据工厂工作室 UI 将 Azure-SSIS 集成运行时加入虚拟网络](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [使用 Azure PowerShell 将 Azure-SSIS 集成运行时加入虚拟网络](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

有关 Azure-SSIS IR 的详细信息，请参阅以下文章： 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 此文提供有关 IR（包括 Azure-SSIS IR）的一般概念性信息。 
- [教程：将 SSIS 包部署到 Azure](./tutorial-deploy-ssis-packages-azure.md) 中的分步说明创建一个。 此教程提供有关创建 Azure-SSIS IR 的分步说明。 它使用 Azure SQL 数据库来托管 SSIS 目录。 
- [创建 Azure-SSIS IR](create-azure-ssis-integration-runtime.md)。 此文对本教程的内容做了扩充。 其中说明了如何使用具有虚拟网络服务终结点的 Azure SQL 数据库或虚拟网络中的 SQL 托管实例来托管 SSIS 目录。 此外，它介绍了如何将 Azure-SSIS IR 加入虚拟网络。 
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 此文介绍如何获取有关 Azure-SSIS IR 的信息。 其中提供了返回的信息的状态说明。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)。 此文介绍如何停止、启动或删除 Azure-SSIS IR。 此外，介绍如何通过添加节点来扩展 Azure-SSIS IR。

