---
title: 为 Azure Key Vault 托管 HSM 配置专用终结点
description: 了解如何集成 Azure Key Vault 托管 HSM 与 Azure 专用链接服务
author: mbaldwin
ms.author: mbaldwin
ms.date: 06/21/2021
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: d9dff0d6d9d8421e160c19c60efc9e871d7867ed
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443567"
---
# <a name="integrate-managed-hsm-with-azure-private-link"></a>将托管 HSM 与 Azure 专用链接集成

使用 Azure 专用链接服务，可以通过虚拟网络中的专用终结点访问 Azure 服务（例如托管 HSM、Azure 存储和 Azure Cosmos DB 等）以及 Azure 托管的客户服务/合作伙伴服务。

Azure 专用终结点是一个网络接口，可以通过私密且安全的方式将你连接到 Azure 专用链接支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以连接到 Azure 资源的实例，从而获得最高级别的访问控制粒度。

有关详细信息，请参阅[什么是 Azure 专用链接？](../../private-link/private-link-overview.md)

## <a name="prerequisites"></a>先决条件

若要将托管 HSM 与 Azure 专用链接集成，需要准备好以下各项：

- 一个托管 HSM。 有关详细信息，请参阅[使用 Azure CLI 预配和激活托管 HSM](quick-create-cli.md)。
- 一个 Azure 虚拟网络。
- 虚拟网络中的子网。
- 拥有托管 HSM 和虚拟网络的所有者或参与者权限。
- Azure CLI 版本 2.25.0 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

专用终结点和虚拟网络必须位于同一区域。 使用门户选择专用终结点的区域时，只会自动筛选该区域中的虚拟网络。 HSM 可以位于不同的区域。

专用终结点使用虚拟网络中的专用 IP 地址。


## <a name="establish-a-private-link-connection-to-managed-hsm-using-cli-initial-setup"></a>使用 CLI 建立连接到托管 HSM 的专用链接（初始设置）

```azurecli
az login                                                                   # Login to Azure CLI
az account set --subscription {SUBSCRIPTION ID}                            # Select your Azure Subscription
az group create -n {RESOURCE GROUP} -l {REGION}                            # Create a new Resource Group
az provider register -n Microsoft.KeyVault                                 # Register KeyVault as a provider
az keyvault update-hsm --hsm-name {HSM NAME} -g {RG} --default-action deny # Turn on firewall

az network vnet create -g {RG} -n {vNet NAME} --location {REGION}           # Create a Virtual Network

    # Create a Subnet
az network vnet subnet create -g {RG} --vnet-name {vNet NAME} --name {subnet NAME} --address-prefixes {addressPrefix}

    # Disable Virtual Network Policies
az network vnet subnet update --name {subnet NAME} --resource-group {RG} --vnet-name {vNet NAME} --disable-private-endpoint-network-policies true

    # Create a Private DNS Zone
az network private-dns zone create --resource-group {RG} --name privatelink.managedhsm.azure.net

    # Link the Private DNS Zone to the Virtual Network
az network private-dns link vnet create --resource-group {RG} --virtual-network {vNet NAME} --zone-name privatelink.managedhsm.azure.net --name {dnsZoneLinkName} --registration-enabled true

```

### <a name="allow-trusted-services-to-access-managed-hsm"></a>允许受信任的服务访问托管 HSM

打开防火墙后，系统将拒绝来自未使用专用终结点连接的位置的所有 HSM 访问，包括公共 Internet 和 Azure 服务。 如果想要允许 Microsoft 服务访问托管 HSM 中的密钥，请使用 `--baypss AzureServices` 选项。 单个实体（例如 Azure 存储帐户或 Azure SQL Server）仍需要完成特定的角色分配，才能访问密钥。 

> [!NOTE]
> 仅支持特定的受信任服务使用方案。 有关更多详细信息，请参阅[受信任服务使用方案列表](../general/overview-vnet-service-endpoints.md#trusted-services)。

```azurecli
az keyvault update-hsm --hsm-name {HSM NAME} -g {RG} --default-action deny --bypass AzureServices
```

### <a name="create-a-private-endpoint-automatically-approve"></a>创建专用终结点（自动批准） 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/managedHSMs/{HSM NAME}" --group-id managedhsm --connection-name {Private Link Connection Name} --location {AZURE REGION}
```

> [!NOTE]
> 如果删除此 HSM，专用终结点将停止工作。 如果稍后恢复（取消删除）此 HSM，则必须重新创建新的专用终结点。

### <a name="create-a-private-endpoint-manually-request-approval"></a>创建专用终结点（手动请求批准） 
```azurecli
az network private-endpoint create --resource-group {RG} --vnet-name {vNet NAME} --subnet {subnet NAME} --name {Private Endpoint Name}  --private-connection-resource-id "/subscriptions/{AZURE SUBSCRIPTION ID}/resourceGroups/{RG}/providers/Microsoft.KeyVault/managedHSMs/{HSM NAME}" --group-id managedhsm --connection-name {Private Link Connection Name} --location {AZURE REGION} --manual-request
```

### <a name="manage-private-link-connections"></a>管理专用链接连接

```azurecli
# Show Connection Status
az network private-endpoint show --resource-group {RG} --name {Private Endpoint Name}

# Approve a Private Link Connection Request
az keyvault private-endpoint-connection approve --description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --hsm-name {HSM NAME} –-name {PRIVATE LINK CONNECTION NAME}

# Deny a Private Link Connection Request
az keyvault private-endpoint-connection reject --description {"OPTIONAL DESCRIPTION"} --resource-group {RG} --hsm-name {HSM NAME} –-name {PRIVATE LINK CONNECTION NAME}

# Delete a Private Link Connection Request
az keyvault private-endpoint-connection delete --resource-group {RG} --hsm-name {HSM NAME} --name {PRIVATE LINK CONNECTION NAME}
```

### <a name="add-private-dns-records"></a>添加专用 DNS 记录
```azurecli
# Determine the Private Endpoint IP address
az network private-endpoint show -g {RG} -n {PE NAME}      # look for the property networkInterfaces then id; the value must be placed on {PE NIC} below.
az network nic show --ids {PE NIC}                         # look for the property ipConfigurations then privateIpAddress; the value must be placed on {NIC IP} below.

# https://docs.microsoft.com/en-us/azure/dns/private-dns-getstarted-cli#create-an-additional-dns-record
az network private-dns zone list -g {RG}
az network private-dns record-set a add-record -g {RG} -z "privatelink.managedhsm.azure.net" -n {HSM NAME} -a {NIC IP}
az network private-dns record-set list -g {RG} -z "privatelink.managedhsm.azure.net"

# From home/public network, you wil get a public IP. If inside a vnet with private zone, nslookup will resolve to the private ip.
nslookup {HSM NAME}.managedhsm.azure.net
nslookup {HSM NAME}.privatelink.managedhsm.azure.net
```

---

## <a name="validate-that-the-private-link-connection-works"></a>验证专用链接连接是否有效

应该验证专用终结点资源的同一子网中的资源是否可以通过专用 IP 地址连接到 HSM，以及它们是否具有正确的专用 DNS 区域集成。

首先，遵循[在 Azure 门户中创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-portal.md)中的步骤创建一个虚拟机。

在“网络”选项卡中：

1. 指定虚拟网络和子网。 可以创建新的或选择现有的虚拟网络。 如果选择现有的虚拟网络，请确保区域匹配。
1. 指定公共 IP 资源。
1. 在“NIC 网络安全组”中选择“无”。
1. 在“负载均衡”中选择“否”。

打开命令行并运行以下命令：

```console
nslookup <your-HSM-name>.managedhsm.azure.net
```

如果运行 ns lookup　命令以通过公共终结点解析托管 HSM 的 IP 地址，你将会看到如下所示的结果：

```console
c:\ >nslookup <your-hsm-name>.managedhsm.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-hsm-name>.managedhsm.azure.net
```

如果运行 ns lookup　命令以通过专用终结点解析托管 HSM 的 IP 地址，你将会看到如下所示的结果：

```console
c:\ >nslookup your_hsm_name.managedhsm.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-hsm-name>.managed.azure.net
          <your-hsm-name>.privatelink.managedhsm.azure.net
```

## <a name="troubleshooting-guide"></a>故障排除指南

* 检查以确保专用终结点处于已批准状态。 
    1. 使用 ```az keyvault private-endpoint-connections show``` 子命令查看专用终结点连接的状态。
    2. 请确保连接状态为“已批准”并且预配状态为“成功”。 
    3. 请确保虚拟网络与你正在使用的网络匹配。

* 请检查以确保具有专用 DNS 区域资源。 
    1. 必须具有名称恰好是 privatelink.managedhsm.azure.net 的专用 DNS 区域资源。 
    2. 若要了解如何对此进行设置，请参阅以下链接。 [专用 DNS 区域](../../dns/private-dns-privatednszone.md)
    
* 请检查确保专用 DNS 区域已与虚拟网络关联。 如果仍在返回公共 IP 地址，则可能存在该问题。 
    1. 如果专用区域 DNS 未与虚拟网络关联，则从虚拟网络发起的 DNS 查询将返回 HSM 的公共 IP 地址。 
    2. 在 Azure 门户中导航到专用 DNS 区域资源，然后单击虚拟网络链接选项。 
    4. 必须列出将对 HSM 执行调用的虚拟网络。 
    5. 如果它不存在，请添加它。 
    6. 有关详细步骤，请参阅以下文档[将虚拟网络链接到专用 DNS 区域](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)

* 请检查以确保专用 DNS 区域中包含 HSM 的 A 记录。 
    1. 导航到专用 DNS 区域页。 
    2. 单击“概述”，检查是否存在 HSM 简单名称的 A 记录。 不要指定任何后缀。
    3. 请务必检查拼写，创建或修复 A 记录。 可以使用 3600（1小时）的 TTL。 
    4. 确保指定了正确的专用 IP 地址。 
    
* 检查以确保 A 记录具有正确的 IP 地址。 
    1. 可在 Azure 门户中打开专用终结点资源来确认 IP 地址。
    2. 在 Azure 门户中，导航到 Microsoft.Network/privateEndpoints 资源
    3. 在概述页中，查找网络接口，然后单击该链接。 
    4. 该链接显示 NIC 资源的概述，其中包含专用 IP 地址属性。 
    5. 验证这是否为在 A 记录中指定的正确 IP 地址。

## <a name="limitations-and-design-considerations"></a>限制和设计注意事项

> [!NOTE]
> 按订阅启用了专用终结点的托管 HSM 数量可调整限制。 下面显示的限制是默认限制。 若要请求增加订阅限制，请创建 Azure 支持票证。 我们将根据具体情况审批这些请求。

**定价**：有关定价信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link/)。

每个托管 HSM 的专用终结点数目上限：64。

每个订阅的包含专用终结点的默认托管 HSM 数目：400。

有关详细信息，请参阅 [Azure 专用链接服务：限制](../../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 专用链接](../../private-link/private-link-service-overview.md)
- 详细了解[托管 HSM](overview.md)
