---
title: 为 Azure HDInsight 群集创建虚拟网络
description: 了解如何创建 Azure 虚拟网络，以将 HDInsight 连接到其他云资源或数据中心内的资源。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli, devx-track-azurepowershell
ms.date: 05/12/2021
ms.openlocfilehash: 642ec1908298c331777776f886e005e715a727f2
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730633"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>为 Azure HDInsight 群集创建虚拟网络

本文通过操作示例与代码示例来演示如何创建和配置 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)。 与 Azure HDInsight 群集配合使用。 其中提供了有关创建网络安全组 (NSG) 和配置 DNS 的详细示例。

有关在 Azure HDInsight 中使用虚拟网络的背景信息，请参阅[规划 Azure HDInsight 的虚拟网络](hdinsight-plan-virtual-network-deployment.md)。

## <a name="prerequisites-for-code-samples-and-examples"></a>代码示例和示例的先决条件

在执行本文中的任何代码示例之前，请先了解 TCP/IP 网络。 如果不熟悉 TCP/IP 网络，请在修改生产网络之前咨询相关的人员。

本文中的示例要求满足的其他先决条件包括：

* 如果使用的是 PowerShell，则需要安装 [Az 模块](/powershell/azure/)。
* 若要使用 Azure CLI 但尚未安装它，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

> [!IMPORTANT]  
> 如果正在查找有关如何使用 Azure 虚拟网络将 HDInsight 连接到本地网络的分步指南，请参阅[将 HDInsight 连接到本地网络](connect-on-premises-network.md)文档。

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>示例：网络安全组与 HDInsight

此部分中的示例演示如何创建网络安全组规则。 这些规则允许 HDInsight 与 Azure 管理服务通信。 在使用示例之前，请调整 IP 地址，使之与所用 Azure 区域的 IP 地址匹配。 在 [HDInsight 管理 IP 地址](hdinsight-management-ip-addresses.md)中可找到此信息。

### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

以下资源管理器模板创建一个虚拟网络，该网络限制入站流量，但允许来自 HDInsight 所需的 IP 地址的流量。 该模板还在虚拟网络中创建 HDInsight 群集。

* [部署安全的 Azure 虚拟网络和 HDInsight Hadoop 群集](https://azure.microsoft.com/resources/templates/hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

使用以下 PowerShell 脚本创建可限制入站流量的虚拟网络，但允许来自北欧区域的 IP 地址的流量。

> [!IMPORTANT]  
> 更改此示例中的 `hdirule1` 和 `hdirule2` 的 IP 地址，使之与要使用的 Azure 区域匹配。 在 [HDInsight 管理 IP 地址](hdinsight-management-ip-addresses.md)中可找到此信息。

```azurepowershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

此示例演示如何添加规则，以便在所需的 IP 地址上允许入站流量。 它不包含用于限制从其他源的入站访问的规则。 以下代码演示如何允许来自 Internet 的 SSH 访问：

```azurepowershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Azure CLI

使用以下步骤创建一个虚拟网络，该网络限制入站流量，但允许来自 HDInsight 所需的 IP 地址的流量。

1. 使用以下命令创建名为 `hdisecure` 的新网络安全组。 将 `RESOURCEGROUP` 替换为包含 Azure 虚拟网络的资源组。 将 `LOCATION` 替换为组创建在的位置（区域）。

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    在创建组后，会收到有关新组的信息。

2. 使用以下命令将规则添加新网络安全组，以允许从 Azure HDInsight 运行状况和管理服务通过端口 443 发起的入站通信。 将 `RESOURCEGROUP` 替换为包含 Azure 虚拟网络的资源组的名称。

    > [!IMPORTANT]  
    > 更改此示例中的 `hdirule1` 和 `hdirule2` 的 IP 地址，使之与要使用的 Azure 区域匹配。 在 [HDInsight 管理 IP 地址](hdinsight-management-ip-addresses.md)中可找到此信息。

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. 若要检索此网络安全组的唯一标识符，请使用以下命令：

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    此命令返回类似于以下文本的值：

    ```output
    "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

4. 使用以下命令将网络安全组应用于子网。 将 `GUID` 和 `RESOURCEGROUP` 值替换为从上一步骤中返回的值。 将 `VNETNAME` 和 `SUBNETNAME` 替换为要创建的虚拟网络名称和子网名称。

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    此命令完成后，即可将 HDInsight 安装到虚拟网络中。

这些步骤只会实现对 Azure 云中 HDInsight 运行状况和管理服务的访问。 任何从虚拟网络外部对 HDInsight 群集的其他访问会被阻止。 若要从虚拟网络之外启用访问，必须添加其他的虚拟网络安全组规则。

以下代码演示如何允许来自 Internet 的 SSH 访问：

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a> 示例：DNS 配置

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>在虚拟网络和连接的本地网络之间进行名称解析

此示例作出以下假设：

* 你有一个使用 VPN 网关连接到本地网络的 Azure 虚拟网络。

* 虚拟网络中的自定义 DNS 服务器运行 Linux 或 Unix 作为操作系统。

* [Bind](https://www.isc.org/downloads/bind/) 安装在自定义 DNS 服务器上。

在虚拟网络的自定义 DNS 服务器上，执行以下操作：

1. 使用 Azure PowerShell 或 Azure CLI 查找虚拟网络的 DNS 后缀：

    将 `RESOURCEGROUP` 替换为包含虚拟网络的资源组的名称，然后输入命令：

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

1. 在虚拟网络的自定义 DNS 服务器上，使用以下文本作为 `/etc/bind/named.conf.local` 文件的内容：

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    将 `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` 值替换为虚拟网络的 DNS 后缀。

    此配置将针对虚拟网络 DNS 后缀的所有 DNS 请求路由到 Azure 递归解析程序。

1. 在虚拟网络的自定义 DNS 服务器上，使用以下文本作为 `/etc/bind/named.conf.options` 文件的内容：

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * 将 `10.0.0.0/16` 值替换为虚拟网络的 IP 地址范围。 此项允许该范围内的名称解析请求地址。

    * 将本地网络的 IP 地址范围添加到 `acl goodclients { ... }` 部分。  此项允许本地网络中的资源发出的名称解析请求。
    
    * 将值 `192.168.0.1` 替换为本地 DNS 服务器的 IP 地址。 此项将所有其他的 DNS 请求路由到本地 DNS 服务器。

1. 若要使用此配置，请重启 Bind。 例如，`sudo service bind9 restart`。

1. 向本地 DNS 服务器添加条件转发器。 配置条件转发器，将对步骤 1 中 DNS 后者的请求发送到自定义 DNS 服务器。

    > [!NOTE]  
    > 请参阅 DNS 软件的文档，详细了解如何添加条件转发器。

完成这些步骤后，即可使用完全限定的域名 (FQDN) 连接到任一网络中的资源。 现在可以将 HDInsight 安装到虚拟网络。

### <a name="name-resolution-between-two-connected-virtual-networks"></a>在两个连接的虚拟网络之间进行名称解析

此示例作出以下假设：

* 你有两个使用 VPN 网关或对等互连进行连接的 Azure 虚拟网络。

* 两个网络中的自定义 DNS 服务器运行 Linux 或 Unix 作为操作系统。

* [Bind](https://www.isc.org/downloads/bind/) 安装在自定义 DNS 服务器上。

1. 使用 Azure PowerShell 或 Azure CLI 查找两个虚拟网络的 DNS 后缀：

    将 `RESOURCEGROUP` 替换为包含虚拟网络的资源组的名称，然后输入命令：

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. 在自定义 DNS 服务器上，使用以下文本作为 `/etc/bind/named.config.local` 文件的内容。 在两个虚拟网络中对自定义 DNS 服务器进行这种更改。

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    将 `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` 值替换为另一虚拟网络的 DNS 后缀。 此项将对远程网络 DNS 后缀的请求路由到该网络中的自定义 DNS。

3. 在两个虚拟网络的自定义 DNS 服务器上，使用以下文本作为 `/etc/bind/named.conf.options` 文件的内容：

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

   将 `10.0.0.0/16` 和 `10.1.0.0/16` 值替换为虚拟网络的 IP 地址范围。 此项允许每个网络中的资源发出 DNS 服务器的请求。

    不是针对虚拟网络 DNS 后缀（例如，microsoft.com）的任何请求由 Azure 递归解析程序处理。

4. 若要使用此配置，请重启 Bind。 例如，两个 DNS 服务器上的 `sudo service bind9 restart`。

完成这些步骤后，即可使用完全限定的域名 (FQDN) 连接到虚拟网络中的资源。 现在可以将 HDInsight 安装到虚拟网络。

## <a name="test-your-settings-before-deploying-an-hdinsight-cluster"></a>部署 HDInsight 群集之前，请测试你的设置

在部署群集之前，可以通过在与规划的群集相同的 VNet 和子网中的虚拟机上运行 [networkValidator 工具](https://github.com/Azure-Samples/hdinsight-diagnostic-scripts/blob/main/HDInsightNetworkValidator)来检查许多网络配置设置是否正确。

部署虚拟机以运行 networkValidator.sh 脚本

1. 打开 [Azure 门户 Ubuntu Server 18.04 LTS 页](https://portal.azure.com/?feature.customportal=false#create/Canonical.UbuntuServer1804LTS-ARM)并单击“创建”。

1. 在“基本信息”选项卡中的“项目详细信息”下，选择你的订阅，然后选择现有资源组或创建一个新的资源组 。

    :::image type="content" source="./media/hdinsight-create-virtual-network/project-details.png" alt-text="“项目详细信息”部分的屏幕截图，显示为虚拟机选择 Azure 订阅和资源组的位置。":::

1. 在“实例详细信息”下，输入唯一的虚拟机名称，选择与 VNet 相同的区域，对于“可用性选项”，选择“无需基础结构冗余”，为映像选择“Ubuntu 18.04 LTS”，将“Azure 现成实例”留空，然后为“大小”选择 Standard_B1s（或更大）    。

    :::image type="content" source="./media/hdinsight-create-virtual-network/instance-details.png" alt-text="“实例详细信息”部分的屏幕截图，可在其中提供虚拟机的名称并选择其区域、映像和大小。":::

1. 在“管理员帐户”下，选择“密码”并输入管理员帐户的用户名和密码 。 

    :::image type="content" source="./media/hdinsight-create-virtual-network/administrator-account.png" alt-text="管理员帐户部分的屏幕截图，可以在其中选择身份验证类型并提供管理员凭据。":::

1. 在“入站端口规则” > “公共入站端口”下，选择“允许所选端口”，然后从下拉列表中选择“SSH (22)”，并选择“下一步: 磁盘 >”    

    :::image type="content" source="./media/hdinsight-create-virtual-network/inbound-port-rules.png" alt-text="“入站端口规则”部分的屏幕截图，可在其中选择允许建立入站连接的端口。":::

1. 在“磁盘选项”下，选择“标准 SSD”作为磁盘类型，然后单击“下一步:网络 >”。

1. 在“网络”页的“网络接口”下，选择要在其中添加 HDInsight 群集的“虚拟网络”和“子网”，然后选择页面底部的“查看 + 创建”    。

    :::image type="content" source="./media/hdinsight-create-virtual-network/vnet.png" alt-text="选择要在其中添加虚拟机的 VNet 和子网的网络接口部分的屏幕截图。":::

1. 在“创建虚拟机”页上，可以查看要创建的 VM 的详细信息。 准备好以后，选择“创建”。

1. 部署完成后，选择“转到资源”。

1. 在新 VM 的页面上，选择公共 IP 地址并将其复制到剪贴板。

    :::image type="content" source="./media/hdinsight-create-virtual-network/ip-address.png" alt-text="显示如何复制虚拟机的 IP 地址的屏幕截图。":::

运行 /networkValidator.sh script

1. 通过 SSH 连接到新的虚拟机。
1. 使用以下命令将所有文件从 [github](https://github.com/Azure-Samples/hdinsight-diagnostic-scripts/tree/main/HDInsightNetworkValidator) 复制到虚拟机：

    `wget -i https://raw.githubusercontent.com/Azure-Samples/hdinsight-diagnostic-scripts/main/HDInsightNetworkValidator/all.txt`

1. 在文本编辑器中打开 params.txt 文件，并向所有变量添加值。 如果要省略相关验证，请使用空字符串 ("")。
1. 运行 `sudo chmod +x ./setup.sh` 以使 setup.sh 可执行并使用 `sudo ./setup.sh` 运行它以安装适用于 Python 2.x 的 pip 并安装所需的 Python 2.x 模块。
1. 使用 `sudo python2 ./networkValidator.py` 运行主脚本。
1. 脚本完成后，摘要部分会指示检查是否成功以及你是否可以创建群集，或者指示是否遇到任何问题，在这种情况下，你应查看错误输出和相关文档以修复错误。

    尝试修复错误后，可以再次运行脚本以检查进度。
1. 检查完毕且摘要显示“成功：可在此 VNet/子网中创建 HDInsight 群集”。 你即可创建自己的群集。 
1. 运行完验证脚本后，请删除新的虚拟机。 

## <a name="next-steps"></a>后续步骤

* 如需通过完整示例来了解如何将 HDInsight 配置为连接到本地网络，请参阅[将 HDInsight 连接到本地网络](./connect-on-premises-network.md)。
* 若要了解如何在 Azure 虚拟网络中配置 Apache HBase 群集，请参阅[在 Azure 虚拟网络中的 HDInsight 上创建 Apache HBase 群集](hbase/apache-hbase-provision-vnet.md)。
* 要了解如何配置 Apache HBase 异地复制，请参阅[在 Azure 虚拟网络中设置 Apache HBase 群集复制](hbase/apache-hbase-replication.md)。
* 有关 Azure 虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。

* 有关网络安全组的详细信息，请参阅[网络安全组](../virtual-network/network-security-groups-overview.md)。

* 有关用户定义的路由的详细信息，请参阅[用户定义的路由和 IP 转发](../virtual-network/virtual-networks-udr-overview.md)。
