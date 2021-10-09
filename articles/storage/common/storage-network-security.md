---
title: 配置 Azure 存储防火墙和虚拟网络 | Microsoft Docs
description: 使用 Azure 存储防火墙和 Azure 虚拟网络为存储帐户配置分层网络安全。
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 06/09/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 82f860b78a8c0c4114a2250912fb6ec22e040d71
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606069"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>配置 Azure 存储防火墙和虚拟网络

Azure 存储提供分层安全模型。 使用此模型可以根据所用网络或资源的类型和子集，来保护和控制应用程序与企业环境所需的存储帐户访问级别。 配置网络规则后，只有通过指定的网络集或指定的 Azure 资源集请求数据的应用程序才能访问存储帐户。 可以仅限源自指定 IP 地址、IP 范围、Azure 虚拟网络 (VNet) 中的子网或某些 Azure 服务的资源实例的请求访问存储帐户。

存储帐户具有可通过 internet 访问的公共终结点。 还可以为[存储帐户创建专用终结点](storage-private-endpoints.md)，该终结点将从 VNet 向存储帐户分配专用 IP 地址，并通过专用链接保护 VNet 和存储帐户之间往来的所有流量。 Azure 存储防火墙为存储帐户的公共终结点提供访问控制。 使用专用终结点时，还可以使用防火墙阻止通过公用终结点进行的所有访问。 通过存储防火墙配置，还可以选择受信任的 Azure 平台服务安全地访问存储帐户。

在网络规则生效后访问存储帐户的应用程序仍需要在请求中提供适当的授权。 支持适用于 Blob 和队列的 Azure Active Directory (Azure AD) 凭据、有效帐户访问密钥或 SAS 令牌提供授权。

> [!IMPORTANT]
> 默认情况下，除非请求源自在 Azure 虚拟网络 (VNet) 中运行的服务或者源自允许的公共 IP 地址，否则启用存储帐户的防火墙规则会阻止数据传入请求。 被阻止的请求包括来自其他 Azure 服务、来自 Azure 门户、来自日志记录和指标服务等的请求。
>
> 通过允许来自托管服务实例的子网的流量，可以授予对在 VNet 内运行的 Azure 服务相应的访问权限。 此外，可以通过下面所述的例外机制，启用有限数量的方案。 若要通过 Azure 门户访问存储帐户中的数据，需要从设置的受信任的边界（IP 或 VNet）内的计算机进行访问。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>方案

若要保护存储帐户，应该先配置一个规则，以便在公共终结点上默认拒绝来自所有网络的流量（包括 Internet 流量）进行访问。 然后，应配置允许访问特定 vnet 流量的规则。 此外，还可以配置规则为来自所选公共 Internet IP 地址范围的流量授予访问权限，以便能够从特定的 Internet 或本地客户端建立连接。 借助此配置，可为应用程序生成安全网络边界。

可在同一存储帐户中，将允许从特定虚拟网络以及从公共 IP 地址范围进行访问的防火墙规则组合到一起。 可对现有的存储帐户应用存储防火墙规则，或者在创建新存储帐户时应用这些规则。

存储防火墙规则适用于存储帐户的公共终结点。 不需要配置任何防火墙访问规则来允许存储帐户的专用终结点的流量。 通过批准专用终结点的创建，可授予对来自托管该专用终结点的子网的流量的隐式访问权限。

对于面向 Azure 存储的所有网络协议（包括 REST 和 SMB），将强制实施网络规则。 要使用 Microsoft Azure 门户、存储资源管理器和 AZCopy 等工具访问数据，必须配置显式网络规则。

一旦应用网络规则，就会对所有请求强制实施这些规则。 用于向特定 IP 地址授予访问权限的 SAS 令牌可限制令牌持有者的访问权限，但不会越过已配置的网络规则授予新的访问权限。

虚拟机磁盘流量（包括装载和卸载操作以及磁盘 IO）不受网络规则影响。 对页 blob 的 REST 访问受网络规则保护。

经典存储帐户不支持防火墙和虚拟网络。

可通过创建例外，使用应用了网络规则的存储帐户中的非托管磁盘来备份和还原 VM。 此过程在本文的[管理例外](#manage-exceptions)部分记录。 防火墙例外不适用于托管磁盘，因为它们已由 Azure 托管。

## <a name="change-the-default-network-access-rule"></a>更改默认网络访问规则

默认情况下，存储帐户接受来自任何网络上客户端的连接。 若要限制为仅允许选定网络访问，必须先更改默认操作。

> [!WARNING]
> 更改网络规则可能会使应用程序无法正常连接到 Azure 存储。 除非还应用了 **授予** 访问权限的特定网络规则，否则将默认网络规则设置为“拒绝”会阻止对数据的所有访问。 在将默认规则更改为拒绝访问之前，务必先使用网络规则对所有许可网络授予访问权限。

### <a name="managing-default-network-access-rules"></a>管理默认网络访问规则

可以通过 Azure 门户、PowerShell 或 CLIv2 管理存储帐户的默认网络访问规则。

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. 转至要保护的存储帐户。

2. 选择名为“网络”的设置菜单。

3. 若要默认拒绝访问，请选择允许从“所选网络”进行访问。 若要允许来自所有网络的流量，请选择允许从“所有网络”进行访问。

4. 单击“保存”应用所做的更改。

<a id="powershell"></a>

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 安装 [Azure PowerShell](/powershell/azure/install-Az-ps) 并 [登录](/powershell/azure/authenticate-azureps)。

2. 显示存储帐户默认规则的状态。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

3. 将默认规则设置为默认拒绝网络访问。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

4. 将默认规则设置为默认允许网络访问。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 安装 [Azure CLI](/cli/azure/install-azure-cli) 并 [登录](/cli/azure/authenticate-azure-cli)。

2. 显示存储帐户默认规则的状态。

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

3. 将默认规则设置为默认拒绝网络访问。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

4. 将默认规则设置为默认允许网络访问。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

---

## <a name="grant-access-from-a-virtual-network"></a>允许从虚拟网络进行访问

可将存储帐户配置为仅允许从特定子网进行访问。 允许的子网可以属于同一订阅中的 VNet，也可以属于不同订阅（包括属于不同 Azure Active Directory 租户的订阅）中的 VNet。

在 VNet 内为 Azure 存储启用[服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)。 服务终结点通过最佳路径将流量从 VNet 路由到 Azure 存储服务。 子网和虚拟网络的标识也随每个请求进行传输。 管理员随后可以配置存储帐户的网络规则，允许从 VNet 中的特定子网接收请求。 通过这些网络规则获得访问权限的客户端必须继续满足存储帐户的授权要求，才能访问数据。

每个存储帐户最多支持 200 条虚拟网络规则，这些规则可与 [IP 网络规则](#grant-access-from-an-internet-ip-range)组合使用。

> [!IMPORTANT]
> 如果删除网络规则中包含的子网，系统将从存储帐户的网络规则中删除该子网。 如果使用相同的名称创建新子网，其将无法访问存储帐户。 若要允许访问，则必须在存储帐户的网络规则中明确授权新子网。

### <a name="available-virtual-network-regions"></a>可用的虚拟网络区域

服务终结点一般在位于同一 Azure 区域的虚拟网络和服务实例之间运行。 将服务终结点与 Azure 存储配合使用时，此范围扩大到包含[配对区域](../../best-practices-availability-paired-regions.md)。 服务终结点可以在区域性故障转移期间提供连续性，并允许访问读取访问权限异地冗余存储 (RA-GRS) 实例。 允许从虚拟网络访问存储帐户的网络规则同样允许访问所有 RA-GRS 实例。

在计划区域性服务中断期间的灾难恢复时，应该在配对区域中提前创建 VNet。 为 Azure 存储启用服务终结点，并提供允许从这些备用虚拟网络进行访问的网络规则。 然后将这些规则应用于异地冗余存储帐户。

> [!NOTE]
> 服务终结点不适用于位于虚拟网络所在区域和指定区域对之外的流量。 可以将允许从虚拟网络访问存储帐户的网络规则仅应用于存储帐户主区域或指定配对区域中的存储帐户。

### <a name="required-permissions"></a>所需的权限

若要向存储帐户应用虚拟网络规则，用户必须对要添加的子网拥有适当的权限。 应用规则的操作可由[存储帐户参与者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)执行，也可由通过自定义 Azure 角色获得 `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` [Azure 资源提供程序操作](../../role-based-access-control/resource-provider-operations.md#microsoftnetwork)权限的用户来执行。

存储帐户和获得访问权限的虚拟网络可以位于不同的订阅中，包括属于不同 Azure AD 租户的订阅。

> [!NOTE]
> 目前，仅支持通过 Powershell、CLI 和 REST API 来配置对属于不同 Azure Active Directory 租户的虚拟网络中的子网授予访问权限的规则。 无法通过 Azure 门户配置此类规则，但可以在门户中查看此类规则。

### <a name="managing-virtual-network-rules"></a>管理虚拟网络规则

可以通过 Azure 门户、PowerShell 或 CLIv2 管理存储帐户的虚拟网络规则。

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. 转至要保护的存储帐户。

2. 选择名为“网络”的设置菜单。

3. 检查并确保已选择允许从“所选网络”进行访问。

4. 若要使用新的网络规则授予对虚拟网络的访问权限，请在“虚拟网络”下依次选择“添加现有虚拟网络”、“虚拟网络”和“子网”选项、“添加”    。 若要创建新的虚拟网络并向其授予访问权限，请选择“添加新的虚拟网络”。 提供创建新的虚拟网络所需的信息，然后选择“创建”。

    > [!NOTE]
    > 如果之前没有为所选的虚拟网络和子网配置 Azure 存储的服务终结点，则可在执行此操作时进行配置。
    >
    > 目前，在创建规则期间，只会显示属于同一 Azure Active Directory 租户的虚拟网络供用户选择。 若要对属于其他租户的虚拟网络中的子网授予访问权限，请使用 Powershell、CLI 或 REST API。

5. 若要删除虚拟网络或子网规则，请选择“...”打开虚拟网络或子网的上下文菜单，然后选择“删除” 。

6. 选择“保存”以应用所做的更改。

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 安装 [Azure PowerShell](/powershell/azure/install-Az-ps) 并 [登录](/powershell/azure/authenticate-azureps)。

2. 列出虚拟网络规则。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

3. 在现有虚拟网络和子网上启用 Azure 存储的服务终结点。

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

4. 为虚拟网络和子网添加网络规则。

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > 若要为属于其他 Azure AD 租户的 VNet 中的子网添加网络规则，请使用“/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name”格式的完全限定的 **VirtualNetworkResourceId** 参数。

5. 为虚拟网络和子网删除网络规则。

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则网络规则不会有任何效果。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 安装 [Azure CLI](/cli/azure/install-azure-cli) 并 [登录](/cli/azure/authenticate-azure-cli)。

2. 列出虚拟网络规则。

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

3. 在现有虚拟网络和子网上启用 Azure 存储的服务终结点。

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

4. 为虚拟网络和子网添加网络规则。

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > 若要为属于其他 Azure AD 租户的 VNet 中的子网添加规则，请使用“/subscriptions/\<subscription-ID\>/resourceGroups/\<resourceGroup-Name\>/providers/Microsoft.Network/virtualNetworks/\<vNet-name\>/subnets/\<subnet-name\>”格式的完全限定的子网 ID。
    >
    > 可以使用 **subscription** 参数检索属于其他 Azure AD 租户的 VNet 的子网 ID。

5. 为虚拟网络和子网删除网络规则。

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则网络规则不会有任何效果。

---

## <a name="grant-access-from-an-internet-ip-range"></a>允许从 Internet IP 范围进行访问

可以通过创建 IP 网络规则来使用 IP 网络规则允许从特定的公共 Internet IP 地址范围进行访问。 每个存储帐户最多支持 200 个规则。 这些规则向基于 Internet 的特定服务和本地网络授予访问权限，并阻止一般 Internet 流量。

以下限制适用于 IP 地址范围。

- IP 网络规则仅适用于公共 Internet IP 地址。

  IP 规则不允许使用为专用网络保留的 IP 地址范围（如 [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3) 中所定义）。 专用网络包括以 *10.**、*172.16.** - *172.31.** 和 *192.168.** 开头的地址。

- 必须使用 [CIDR 表示法](https://tools.ietf.org/html/rfc4632)以 16.17.18.0/24 的形式，或使用单独的 IP 地址（如 16.17.18.19）提供允许的 Internet 地址范围。

- 不支持使用“/31”或“/32”前缀大小的小型地址范围。 这些范围应使用单独的 IP 地址规则配置。

- 存储防火墙规则的配置仅支持 IPV4 地址。

在以下情况下，不能使用 IP 网络规则：

- 限制对与存储帐户位于同一 Azure 区域中的客户端的访问。

  IP 网络规则对源自与存储帐户相同的 Azure 区域的请求不起作用。 请使用[虚拟网络规则](#grant-access-from-a-virtual-network)来允许相同区域的请求。

- 限制对[配对区域](../../best-practices-availability-paired-regions.md)中的客户端（在具有服务终结点的 VNet 中）的访问。

- 限制对与存储帐户部署在同一区域中的 Azure 服务的访问。

  与存储帐户部署在同一区域中的服务使用专用的 Azure IP 地址进行通信。 因此，不能基于特定的 Azure 服务的公共出站 IP 地址范围来限制对其的访问。

### <a name="configuring-access-from-on-premises-networks"></a>配置从本地网络的访问

若要使用 IP 网络规则授予本地网络访问存储帐户的权限，则必须标识网络所用的面向 Internet 的 IP 地址。 若要获得帮助，请联系网络管理员。

如果是在本地使用 [ExpressRoute](../../expressroute/expressroute-introduction.md)，则在进行公共对等互连或 Microsoft 对等互连时，需标识所用的 NAT IP 地址。 进行公共对等互连时，每条 ExpressRoute 线路默认情况下会使用两个 NAT IP 地址。当流量进入 Microsoft Azure 网络主干时，会向 Azure 服务流量应用这些地址。 进行 Microsoft 对等互连时，所用 NAT IP 地址由客户或服务提供商提供。 若要允许访问服务资源，必须在资源 IP 防火墙设置中允许这些公共 IP 地址。 若要查找公共对等互连 ExpressRoute 线路 IP 地址，请通过 Azure 门户[开具 ExpressRoute 支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 详细了解[适用于 ExpressRoute 公共对等互连和 Microsoft 对等互连的 NAT](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)。

### <a name="managing-ip-network-rules"></a>管理 IP 网络规则

可以通过 Azure 门户、PowerShell 或 CLIv2 管理存储帐户的 IP 网络规则。

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. 转至要保护的存储帐户。

2. 选择名为“网络”的设置菜单。

3. 检查并确保已选择允许从“所选网络”进行访问。

4. 若要向 Internet IP 范围授予访问权限，请在“防火墙” > “地址范围”下输入 IP 地址或地址范围（采用 CIDR 格式） 。

5. 若要删除某个 IP 网络规则，请选择该地址范围旁边的垃圾桶图标。

6. 单击“保存”应用所做的更改。

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 安装 [Azure PowerShell](/powershell/azure/install-Az-ps) 并 [登录](/powershell/azure/authenticate-azureps)。

2. 列出 IP 网络规则。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

3. 为单个 IP 地址添加网络规则。

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

4. 为 IP 地址范围添加网络规则。

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

5. 为单个 IP 地址删除网络规则。

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

6. 为 IP 地址范围删除网络规则。

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则网络规则不会有任何效果。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 安装 [Azure CLI](/cli/azure/install-azure-cli) 并 [登录](/cli/azure/authenticate-azure-cli)。

1. 列出 IP 网络规则。

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

2. 为单个 IP 地址添加网络规则。

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

3. 为 IP 地址范围添加网络规则。

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

4. 为单个 IP 地址删除网络规则。

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

5. 为 IP 地址范围删除网络规则。

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则网络规则不会有任何效果。

---

<a id="grant-access-specific-instances"></a>

## <a name="grant-access-from-azure-resource-instances-preview"></a>授予从 Azure 资源实例进行访问的权限（预览版）

在某些情况下，应用程序可能依赖于无法通过虚拟网络或 IP 地址规则隔离的 Azure 资源。 但是，你仍希望存储帐户仅访问你的应用程序的 Azure 资源并且访问方式是安全的。 可以通过创建资源实例规则来配置存储帐户，允许其访问某些 Azure 服务的特定资源实例。

资源实例可以对存储帐户数据执行的操作的类型取决于资源实例的 Azure 角色分配。 资源实例必须来自存储帐户所在的租户，但可以属于租户中的任何订阅。

> [!NOTE]
> 该功能现为公共预览版，它在所有公共云区域中都可用。

> [!NOTE]
> 资源实例规则目前只能用于 Azure Synapse。 在接下来的几周内，将提供对本文[基于系统分配的托管标识的受信任的访问](#trusted-access-system-assigned-managed-identity)部分列出的其他 Azure 服务的支持。

### <a name="portal"></a>[Portal](#tab/azure-portal)

可以在 Azure 门户中添加或删除资源网络规则。

1. 登录到 [Azure 门户](https://portal.azure.com/)即可开始操作。

2. 找到存储帐户并显示帐户概览。

3. 选择“网络”以显示网络的配置页。

4. 在“资源类型”下拉列表中，选择资源实例的资源类型。

5. 在“实例名称”下拉列表中，选择资源实例。 还可以选择包括活动租户、订阅或资源组中的所有资源实例。

6. 单击“保存”应用所做的更改。 资源实例会出现在网络设置页的“资源实例”部分。

若要删除资源实例，请选择资源实例旁边的删除图标 (:::image type="icon" source="media/storage-network-security/delete-icon.png":::)。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

可以使用 PowerShell 命令添加或删除资源网络规则。

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则网络规则不会有任何效果。

#### <a name="install-the-preview-module"></a>安装预览版模块

安装最新版本的 PowershellGet 模块。 然后关闭 PowerShell 控制台，接着重新将其打开。

```powershell
install-Module PowerShellGet –Repository PSGallery –Force  
```

安装 **Az.Storage** 预览版模块。

```powershell
Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.0.1-preview -AllowClobber -AllowPrerelease -Force 
```

有关如何安装 PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)

#### <a name="grant-access"></a>授予访问权限

添加一项网络规则，该规则授予从资源实例进行访问的权限。

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId

```

通过修改网络规则集一次指定多个资源实例。

```powershell
$resourceId1 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$resourceId2 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mySQLServer"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule (@{ResourceId=$resourceId1;TenantId=$tenantId},@{ResourceId=$resourceId2;TenantId=$tenantId}) 
```

#### <a name="remove-access"></a>删除访问权限

删除一项网络规则，该规则授予从资源实例进行访问的权限。

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Remove-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId  
```

删除所有授予从资源实例进行访问的权限的网络规则。

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule @()  
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>查看允许的资源实例的列表

查看已被授予对存储帐户的访问权限的资源实例的完整列表。

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

$rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
$rule.ResourceAccessRules 
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

可以使用 Azure CLI 命令添加或删除资源网络规则。

#### <a name="install-the-preview-extension"></a>安装预览版扩展

1. 打开 [Azure Cloud Shell](../../cloud-shell/overview.md)，或者，如果已在本地[安装](/cli/azure/install-azure-cli) Azure CLI，请打开命令控制台应用程序，如 Windows PowerShell。

2. 然后，使用以下命令验证已安装的 Azure CLI 版本是否为 `2.13.0` 或以上。

   ```azurecli
   az --version
   ```

   如果 Azure CLI 版本低于 `2.13.0`，则安装更高版本。 请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

3. 键入如下命令以安装预览版扩展。

   ```azurecli
   az extension add -n storage-preview
   ```

#### <a name="grant-access"></a>授予访问权限

添加一项网络规则，该规则授予从资源实例进行访问的权限。

```azurecli
az storage account network-rule add \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="remove-access"></a>删除访问权限

删除一项网络规则，该规则授予从资源实例进行访问的权限。

```azurecli
az storage account network-rule remove \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>查看允许的资源实例的列表

查看已被授予对存储帐户的访问权限的资源实例的完整列表。

```azurecli
az storage account network-rule list \
    -g myResourceGroup \
    --account-name mystorageaccount
```

---

<a id="exceptions"></a>
<a id="trusted-microsoft-services"></a>

## <a name="grant-access-to-trusted-azure-services"></a>授予对受信任的 Azure 服务的访问权限

某些 Azure 服务通过无法包含在网络规则中的网络运行。 可向其中的一部分受信任 Azure 服务授予对存储帐户的访问权限，同时对其他应用保持使用网络规则。 然后，这些受信任的服务会使用强身份验证安全地连接到存储帐户。

可以通过创建网络规则例外来授予对受信任的 Azure 服务的访问权限。 有关分步指南，请参阅本文的[管理例外](#manage-exceptions)部分。

当你授予对受信任的 Azure 服务的访问权限时，你将授予以下访问权限类型：

- 选定操作对订阅中注册的资源的受信任的访问权限。
- 针对资源的、基于系统分配的托管标识的、受信任的访问权限。

<a id="trusted-access-resources-in-subscription"></a>

### <a name="trusted-access-for-resources-registered-in-your-subscription"></a>已在订阅中注册的资源的受信任的访问权限

某些服务的资源 **在注册到订阅** 后，可在 **同一订阅** 中访问存储帐户以执行特定的操作，例如写入日志或备份。  下表描述了每项服务和允许的操作。

| 服务                  | 资源提供程序名称     | 允许的操作                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure 备份             | Microsoft.RecoveryServices | 在 IAAS 虚拟机中运行非托管磁盘的备份和还原。 （不是托管磁盘的必需操作）。 [了解详细信息](../../backup/backup-overview.md)。 |
| Azure Data Box           | Microsoft.DataBox          | 支持使用 Data Box 将数据导入到 Azure。 [了解详细信息](../../databox/data-box-overview.md)。 |
| Azure 开发测试实验室       | Microsoft.DevTestLab       | 自定义映像创建和项目安装。 [了解详细信息](../../devtest-labs/devtest-lab-overview.md)。 |
| Azure 事件网格         | Microsoft.EventGrid        | 启用 Blob 存储事件发布并允许事件网格发布到存储队列。 了解有关 [blob 存储事件](../../event-grid/overview.md#event-sources)和[发布到队列](../../event-grid/event-handlers.md)的信息。 |
| Azure 事件中心         | Microsoft.EventHub         | 使用事件中心捕获功能存档数据。 [了解详细信息](../../event-hubs/event-hubs-capture-overview.md)。 |
| Azure 文件同步          | Microsoft.StorageSync      | 使你能够将本地文件服务器转换为 Azure 文件共享的缓存。 可实现多站点同步、快速灾难恢复和云端备份。 [了解详细信息](../file-sync/file-sync-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | 为新的 HDInsight 群集预配默认文件系统的初始内容。 [了解详细信息](../../hdinsight/hdinsight-hadoop-use-blob-storage.md)。 |
| Azure 导入导出      | Microsoft.ImportExport     | 允许使用 Azure 存储导入/导出服务将数据导入到 Azure 存储或从 Azure 存储导出数据。 [了解详细信息](../../import-export/storage-import-export-service.md)。  |
| Azure Monitor            | Microsoft.Insights         | 允许向受保护的存储帐户写入监视数据，包括资源日志、Azure Active Directory 登录和审核日志，以及 Microsoft Intune 日志。 [了解详细信息](../../azure-monitor/roles-permissions-security.md)。 |
| Azure 网络         | Microsoft.Network          | 以多种方式（包括使用网络观察程序和流量分析服务）存储和分析网络流量日志。 [了解详细信息](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)。 |
| Azure Site Recovery      | Microsoft.SiteRecovery     | 使用启用了防火墙的缓存、源或目标存储帐户时，请启用复制，以实现 Azure IaaS 虚拟机的灾难恢复。  [了解详细信息](../../site-recovery/azure-to-azure-tutorial-enable-replication.md)。 |

<a id="trusted-access-system-assigned-managed-identity"></a>

### <a name="trusted-access-based-on-system-assigned-managed-identity"></a>基于系统分配的托管标识的受信任的访问

下表列出了可访问你的存储帐户数据的服务（如果为这些服务的资源实例授予了相应的权限）。

如果帐户未启用分层命名空间功能，可以通过将 Azure 角色显式分配给每个资源实例的[系统分配的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)来授予权限。 在这种情况下，实例的访问范围对应于分配给托管标识的 Azure 角色。

对于已启用分层命名空间功能的帐户，可以使用相同的技术。 但是，如果将系统分配的托管标识添加到存储帐户中包含的任何目录或 blob 的访问控制列表 (ACL)，则不必分配 Azure 角色。 在这种情况下，实例的访问范围将对应已为系统分配的托管标识授予访问权限的目录或文件。 还可以将 Azure 角色和 ACL 组合在一起。 要详细了解如何将它们组合在一起以授予访问权限，请参阅 [Azure Data Lake Storage Gen2 中的访问控制模型](../blobs/data-lake-storage-access-control-model.md)。

> [!TIP]
> 若要授予对特定资源的访问权限，建议的方法是使用资源实例规则。 若要授予对特定资源实例的访问权限，请参阅本文的[授予从 Azure 资源实例进行访问的权限（预览版）](#grant-access-specific-instances)部分。

| 服务                        | 资源提供程序名称                 | 目的            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure API 管理           | Microsoft.ApiManagement/service        | 使用策略允许 API 管理服务访问防火墙后的存储帐户。 [了解详细信息](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy)。 |
| Azure 认知搜索         | Microsoft.Search/searchServices        | 使认知搜索服务能够访问存储帐户，以进行索引编制、处理和查询。 |
| Azure 认知服务       | Microsoft.CognitiveService/accounts    | 使认知服务能够访问存储帐户。 [了解详细信息](../..//cognitive-services/cognitive-services-virtual-networks.md)。|
| Azure 容器注册表任务 | Microsoft.ContainerRegistry/registries | ACR 任务可以在生成容器映像时访问存储帐户。 |
| Azure 数据工厂             | Microsoft.DataFactory/factories        | 允许通过 ADF 运行时访问存储帐户。 |
| Azure Data Share               | Microsoft.DataShare/accounts           | 允许通过数据共享访问存储帐户。 |
| Azure 开发测试实验室             | Microsoft.DevTestLab/labs              | 允许通过开发测试实验室访问存储帐户。 |
| Azure IoT 中心                  | Microsoft.Devices/IotHubs              | 允许将 IoT 中心的数据写入 Blob 存储。 [了解详细信息](../../iot-hub/virtual-network-support.md#egress-connectivity-from-iot-hub-to-other-azure-resources) |
| Azure 逻辑应用               | Microsoft.Logic/workflows              | 使逻辑应用能够访问存储帐户。 [了解详细信息](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity)。 |
| Azure 机器学习服务 | Microsoft.MachineLearningServices      | 经过授权的 Azure 机器学习工作区将实验输出、模型和日志写入 Blob 存储并读取数据。 [了解详细信息](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources)。 |
| Azure 媒体服务           | Microsoft.Media/mediaservices          | 允许通过媒体服务访问存储帐户。 |
| Azure Migrate                  | Microsoft.Migrate/migrateprojects      | 允许通过 Azure Migrate 访问存储帐户。 |
| Azure Purview                  | Microsoft.Purview/accounts             | 允许 Purview 访问存储帐户。 |
| Azure 远程渲染         | Microsoft.MixedReality/remoteRenderingAccounts | 允许通过远程渲染访问存储帐户。 |
| Azure Site Recovery            | Microsoft.RecoveryServices/vaults      | 允许通过 Site Recovery 访问存储帐户。 |
| Azure SQL 数据库             | Microsoft.Sql                          | 允许将审核数据[写入](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md)防火墙后的存储帐户。 |
| Azure Synapse Analytics        | Microsoft.Sql                          | 允许将 COPY 语句、PolyBase（在专用池中）或 `openrowset` 函数与无服务器池中的外部表结合使用来将数据导入和导出特定 SQL 数据库。 [了解详细信息](../../azure-sql/database/vnet-service-endpoint-rule-overview.md)。 |
| Azure 流分析         | Microsoft.StreamAnalytics              | 用于将流式处理作业中的数据写入 Blob 存储。 [了解详细信息](../../stream-analytics/blob-output-managed-identity.md)。 |
| Azure Synapse Analytics        | Microsoft.Synapse/workspaces           | 允许从 Azure Synapse Analytics 访问 Azure 存储中的数据。 |

## <a name="grant-access-to-storage-analytics"></a>授予对存储分析的访问权限

在某些情况下，需要从网络边界外访问读取资源日志和指标。 配置受信任的服务对存储帐户的访问权限时，可以通过创建网络规则例外来允许对日志文件和/或指标表进行读取访问。 有关分步指南，请参阅下面的“管理例外”部分。 若要详细了解如何使用存储分析，请参阅[使用 Azure 存储分析收集日志和指标数据](./storage-analytics.md)。

<a id="manage-exceptions"></a>

## <a name="manage-exceptions"></a>管理异常

可以通过 Azure 门户、PowerShell 或 Azure CLI v2 管理网络规则例外。

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. 转至要保护的存储帐户。

2. 选择名为“网络”的设置菜单。

3. 检查并确保已选择允许从“所选网络”进行访问。

4. 在“例外”下，选择要允许的例外。

5. 单击“保存”应用所做的更改。

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 安装 [Azure PowerShell](/powershell/azure/install-Az-ps) 并 [登录](/powershell/azure/authenticate-azureps)。

2. 显示存储帐户的网络规则例外。

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

3. 配置存储帐户的网络规则例外。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

4. 删除存储帐户的网络规则例外。

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则，删除例外操作不会有任何效果。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 安装 [Azure CLI](/cli/azure/install-azure-cli) 并 [登录](/cli/azure/authenticate-azure-cli)。

2. 显示存储帐户的网络规则例外。

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

3. 配置存储帐户的网络规则例外。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

4. 删除存储帐户的网络规则例外。

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> 请务必[将默认规则设置](#change-the-default-network-access-rule)为“拒绝”，否则，删除例外操作不会有任何效果。

---

## <a name="next-steps"></a>后续步骤

在[服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md)中了解有关 Azure 网络服务终结点的详细信息。

在 [Azure 存储安全指南](../blobs/security-recommendations.md)中深入了解 Azure 存储安全。
