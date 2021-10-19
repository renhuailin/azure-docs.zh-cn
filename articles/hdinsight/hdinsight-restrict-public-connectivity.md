---
title: 在 Azure HDInsight 中限制公共连接（预览版）
description: 了解如何删除所有出站公共 IP 地址的访问权限
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: 8d4fc269137b9d11ab0db046288f1d548b911d7a
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716243"
---
# <a name="restrict-public-connectivity-in-azure-hdinsight-preview"></a>在 Azure HDInsight 中限制公共连接（预览版）

## <a name="overview"></a>概述
在 Azure HDInsight 的[默认虚拟网络体系结构](./hdinsight-virtual-network-architecture.md)中，HDInsight 资源提供程序 (RP) 通过公用网络来与群集通信。 本文介绍可用于创建受限 HDInsight 群集（其中仅限通过专用网络进行入站连接）的高级控制措施。 如果你可以选择在 HDInsight 群集和相关资源之间建立公共连接，请考虑遵循[在 Azure HDInsight 中控制网络流量](./control-network-traffic.md)中的指导来限制群集的连接。 除了限制公共连接外，我们还将添加对启用了专用链接的依赖项资源的支持，这些资源可配置为与这些群集结合使用。

下图显示了在将 `resourceProviderConnection` 设置为 outbound 时，HDInsight 虚拟网络体系结构可能的样子：

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="使用出站资源提供程序连接的 HDInsight 体系结构示意图":::

> [!NOTE]
> 限制公共连接是启用专用链接的先决条件，不应被视为相同的功能。

## <a name="initialize-a-restricted-cluster"></a>初始化受限群集

默认情况下，HDInsight 资源提供程序使用公共 IP 来与群集建立入站连接。 如果将 `resourceProviderConnection` 网络属性设置为 outbound，则其会反转到 HDInsight 资源提供程序的连接，使得连接始终从群集内部向资源提供程序发起。 在此配置中，如果没有入站连接，则无需在网络安全组 (NSG) 中配置入站服务标记，也无需通过用户定义的路由 (UDR) 绕过防火墙/网络虚拟设备 (NVA)。

创建群集后，应该通过添加受限 HDInsight 群集所需的 DNS 记录来设置适当的 DNS 解析。 以下规范名称 DNS 记录 (CNAME) 可在 Azure 托管的公共 DNS 区域上创建：`azurehdinsight.net`

```dns
<clustername>    CNAME    <clustername>-int
```

若要使用群集 FQDN 访问群集，可以直接使用内部负载均衡器专用 IP，或者根据需要使用自己的专用 DNS 区域（在这种情况下，区域名称必须是 `azurehdinsight.net`）来替代群集终结点。 例如，对于专用 DNS 区域 `azurehdinsight.net`，可根据需要添加专用 IP：

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

> [!NOTE]
> 不建议将受限群集与启用了公共连接的其他群集放在同一个 VNet（具有 `azurehdinsight.net` 的专用 DNS 区域）中，否则可能导致意外的 DNS 解析行为/冲突。

为了简化 DNS 设置，我们将返回 FQDN 和相应的专用 IP 地址作为群集 `GET` 响应的一部分。 可以使用此 PowerShell 代码片段开始。

```powershell
<#
    This script is offered as an example to help get started with automation and can be adjusted based on your needs.
    This script assumes:
    - HDInsight cluster has already been created and the context where this script is run has permissions to read/write resources in the same resource group.
    - Private DNS zone resource exists in the same subscription as the HDInsight cluster.
We recommend that you use the latest version of Az.HDInsight module

#>
$subscriptionId = "<Replace with subscription for deploying HDInsight clusters, and containing private DNS zone resource>"

$clusterName = "<Replace with cluster name>"
$clusterResourceGroupName = "<Replace with resource group name>"

# For example, azurehdinsight.net for public cloud.
$dnsZoneName = "<Replace with private DNS zone name>"
$dnsZoneResourceGroupName = "<Replace with private DNS zone resource group name>"

Connect-AzAccount -SubscriptionId $subscriptionId

# 1. Get cluster endpoints
$clusterEndpoints = $(Get-AzHDInsightCluster -ClusterName $clusterName ` -ResourceGroupName $clusterResourceGroupName).ConnectivityEndpoints

$endpointMapping = @{}

foreach($endpoint in $clusterEndpoints)
{
    $label = $endpoint.Location.ToLower().Replace(".$dnsZoneName".ToLower(), "")
    $ip = $endpoint.PrivateIPAddress

    $endpointMapping.Add($label, $ip)
}

# 2. Confirm DNS zone exists
Get-AzPrivateDnsZone -ResourceGroupName $dnsZoneResourceGroupName -Name $dnsZoneName -ErrorAction Stop

# 3. Update DNS entries for the cluster in the private DNS zone
#    - If the entries already exist, update to the new IP
#    - If the entries do not exist, create them
$recordSets = Get-AzPrivateDnsRecordSet -ZoneName $dnsZoneName -ResourceGroupName $dnsZoneResourceGroupName -RecordType A

foreach($label in $endpointMapping.Keys)
{
    $updateRecord = $null

    foreach($record in $recordSets)
    {
        if($record.Name -eq $label)
        {
            $updateRecord = $record
            break;
        }
        
    }

    if($null -ne $updateRecord)
    {
        $updateRecord.Records[0].Ipv4Address = $endpointMapping[$label]
        Set-AzPrivateDnsRecordSet -RecordSet $updateRecord | Out-Null
    }
    else
    {
        New-AzPrivateDnsRecordSet `
            -ResourceGroupName $dnsZoneResourceGroupName `
            -ZoneName $dnsZoneName `
            -Name $label `
            -RecordType A `
            -Ttl 3600 `
            -PrivateDnsRecord (New-AzPrivateDnsRecordConfig -Ipv4Address $endpointMapping[$label]) | Out-Null
    }
}

```

## <a name="adding-private-link-connectivity-private-endpoints-to-cluster-dependent-resources-optional"></a>将专用链接连接（专用终结点）添加到群集依赖资源（可选）

将 `resourceProviderConnection` 配置为 outbound 还可以使用专用终结点访问特定于群集的资源，例如存储（Azure Data Lake Storage Gen2 和 Windows Azure 存储 Blob）、SQL 元存储（Apache Ranger、Ambari、Oozie 和 Hive）和 Azure 密钥保管库。 不强制对这些资源使用专用终结点，但如果你打算对这些资源使用专用终结点，则必须创建这些资源，配置专用终结点和 DNS 条目，然后再创建 HDInsight 群集。 应该可以通过专用终结点或其他方式从群集子网内部访问所有这些资源。
通过专用终结点连接到 Azure Data Lake Storage Gen2 时，请确保 Gen2 存储帐户为“blob”和“dfs”设置了终结点。 有关详细信息，请参阅[创建专用终结点](../private-link/create-private-endpoint-portal.md)。

## <a name="using-firewall-optional"></a>使用防火墙（可选）
HDInsight 群集仍可连接到公共 Internet 以获取出站依赖项。 如果你想要进一步进行限制，可以[配置防火墙](./hdinsight-restrict-outbound-traffic.md)，但并非一定要这样做。

## <a name="how-to-create-clusters"></a>如何创建群集？
### <a name="use-arm-template-properties"></a>使用 ARM 模板属性

下面的 JSON 代码片段包含两个网络属性，你需要在 ARM 模板中配置这两个网络属性，以创建专用 HDInsight 群集。

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound"
}
```

有关包含许多 HDInsight 企业安全功能（包括专用链接）的完整模板，请参阅“[HDInsight 企业安全模板](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)”。

### <a name="use-azure-powershell"></a>使用 Azure PowerShell

若要使用 PowerShell，请参阅[此处](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)的示例。

### <a name="use-azure-cli"></a>使用 Azure CLI
若要使用 Azure CLI，请参阅[此处](/cli/azure/hdinsight#az_hdinsight_create-examples)的示例。

## <a name="next-steps"></a>后续步骤

* [在群集上启用专用链接](./hdinsight-private-link.md)
* [适用于 Azure HDInsight 的企业安全性套餐](enterprise-security-package.md)
* [Azure HDInsight 中的企业安全性常规信息和准则](./domain-joined/general-guidelines.md)
