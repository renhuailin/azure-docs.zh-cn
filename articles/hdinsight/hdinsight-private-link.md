---
title: 通过专用链接（预览）保护和隔离 Azure HDInsight 群集
description: 了解如何使用 Azure 专用链接隔离虚拟网络中的 Azure HDInsight 群集。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 4c5e30bfd7afd8a7cd8974544324f6e610736846
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113438469"
---
# <a name="secure-and-isolate-azure-hdinsight-clusters-with-private-link-preview"></a>通过专用链接（预览）保护和隔离 Azure HDInsight 群集

在 Azure HDInsight 的 [默认虚拟网络体系结构](./hdinsight-virtual-network-architecture.md)中，HDInsight 资源提供程序 (RP) 会使用公共 IP 地址与群集进行通信。 某些方案需要完全的网络隔离，无需使用公共 IP 地址。 本文介绍了可用于创建专用 HDInsight 群集的高级控件。 若要了解如何在不完全隔离网络的情况下限制进出群集的流量，请参阅“[在 Azure HDInsight 中控制网络流量](./control-network-traffic.md)”。

可以配置 Azure 资源管理器 (ARM) 模板中的特定网络属性，从而创建专用 HDInsight 群集。 你可以使用两个属性来创建专用 HDInsight 群集：

* 将 `resourceProviderConnection` 设置为出站，从而删除公共 IP 地址。
* 启用 Azure 专用链接，并通过将 `privateLink` 设置为“启用”来使用[专用终结点](../private-link/private-endpoint-overview.md)。

## <a name="remove-public-ip-addresses"></a>删除公共 IP 地址

默认情况下，HDInsight RP 会使用公共 IP 实现到群集的 *入站* 连接。 如果将 `resourceProviderConnection` 网络属性设置为“*出站*”，则其会反转到 HDInsight RP 的连接，使得连接始终从群集内部向 RP 发起。 没有入站连接，就不需要入站服务标记或公共 IP 地址。

默认虚拟网络体系结构中使用的基本负载均衡器会自动提供公共 NAT（网络地址转换），以便访问所需的出站依赖项，如 HDInsight RP。 如果要限制到公共 internet 的出站连接，可以[配置防火墙](./hdinsight-restrict-outbound-traffic.md)，但这不作要求。

将 `resourceProviderConnection` 配置为出站还允许使用专用终结点访问特定于群集的资源，如 Azure Data Lake Storage Gen2 或外部元存储。 不强制使用这些资源的专用终结点，但如果你计划为这些资源提供专用终结点，则必须配置创建 HDInsight 群集时的专用终结点和 DNS 条目 `before`。 建议在创建群集时创建并提供所需的所有外部 SQL 数据库，如 Apache Ranger、Ambari、Oozie 和 Hive 元存储。 要求是，所有这些资源都必须可通过其自带的专用终结点或以其他方式，从群集子网内部进行访问。

通过专用终结点连接到 Azure Data Lake Storage Gen2 时，请确保 Gen2 存储帐户为“blob”和“dfs”设置了终结点。 有关详细信息，请参阅[创建专用终结点](../storage/common/storage-private-endpoints.md)。

下图显示了在将 `resourceProviderConnection` 设置为“出站”时，HDInsight 虚拟网络体系结构可能的样子：

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="使用出站资源提供程序连接的 HDInsight 体系结构示意图":::

创建群集后，应设置正确的 DNS 解析。 以下规范名称 DNS 记录（CNAME） 可在 Azure 托管的公共 DNS 区域上创建：`azurehdinsight.net`。

```dns
<clustername>    CNAME    <clustername>-int
```

若要使用群集 FQDN 访问群集，可以直接使用内部负载均衡器专用 IP，或者根据需要使用自己的专用 DNS 区域替代群集终结点。 例如，可以有一个专用 DNS 区域，`azurehdinsight.net`。 并根据需要添加专用 IP：

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

## <a name="enable-private-link"></a>启用专用链接

使用默认情况下禁用的“专用链接”需要广泛的网络知识，以便在创建群集之前正确设置用户定义路由 (UDR) 和防火墙规则。 如前一部分中所述，可以选择使用此设置，但仅当 `resourceProviderConnection` 网络属性设置为“*出站*”时，该选项才可用。

当 `privateLink` 设置为“*启用*”时，会创建内部 [标准负均衡器](../load-balancer/load-balancer-overview.md) (SLB) ，并为每个 SLB 预配 Azure 专用链接服务。 专用链接服务可让你从专用终结点访问 HDInsight 群集。

标准负载均衡器不会像基本负载均衡器那样自动提供[公共出站 NAT](../load-balancer/load-balancer-outbound-connections.md)。 对于出站依赖项，你必须提供自己的 NAT 解决方案，如[虚拟网络 NAT](../virtual-network/nat-gateway/nat-overview.md) 或[防火墙](./hdinsight-restrict-outbound-traffic.md)。 你的 HDInsight 群集仍需要其出站依赖项的访问权限。 如果不允许这些出站依赖项，则群集创建可能会失败。

### <a name="prepare-your-environment"></a>准备环境

若要成功创建专用链接服务，必须显式[禁用专用链接服务的网络策略](../private-link/disable-private-link-service-network-policy.md)。

下图显示了创建群集之前所需的网络配置的示例。 在此示例中，将使用 UDR 将所有出站流量[强制](../firewall/forced-tunneling.md)发送到 Azure 防火墙，并且在创建群集之前，所需的出站依赖项在防火墙上应设置为“允许”。 对于企业安全性套餐群集，可通过 VNet 对等互连提供与 Azure Active Directory 域服务的网络连接。

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="群集创建前的专用链接环境示意图":::

如下图所示，设置网络后，可以创建启用了出站资源提供程序连接和专用链接的群集。 在此配置中，没有使用任何公共 IP，并且为每个标准负载均衡器预配了专用链接服务。

:::image type="content" source="media/hdinsight-private-link/after-cluster-creation.png" alt-text="群集创建后的专用链接环境示意图":::

### <a name="access-a-private-cluster"></a>访问专用群集

若要访问专用群集，可以直接使用内部负载均衡器专用 IP，也可以使用专用链接 DNS 扩展和专用终结点。 当 `privateLink` 设置为“启用”时，你可以创建自己的专用终结点，并通过专用 DNS 区域配置 DNS 解析。

在 Azure 托管的公共 DNS 区域 `azurehdinsight.net` 中创建的专用链接条目如下所示：

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```

下图显示了从虚拟网络访问群集所需的专用 DNS 条目的示例，该网络不对等互连，或者没有到群集负载均衡器的直接可视通路。 你可以使用 Azure 专用区域替代 `*.privatelink.azurehdinsight.net`FQDN 并解析为你自己的专用终结点 IP 地址。

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="专用链接体系结构示意图":::

## <a name="how-to-create-clusters"></a>如何创建群集？
### <a name="use-arm-template-properties"></a>使用 ARM 模板属性

下面的 JSON 代码片段包含两个网络属性，你需要在 ARM 模板中配置这两个网络属性，以创建专用 HDInsight 群集。

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled" | "Disabled"
}
```

有关包含许多 HDInsight 企业安全功能（包括专用链接）的完整模板，请参阅“[HDInsight 企业安全模板](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)”。

### <a name="use-azure-powershell"></a>使用 Azure PowerShell

若要使用 PowerShell，请参阅[此处](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)的示例。

### <a name="use-azure-cli"></a>使用 Azure CLI
若要使用 Azure CLI，请参阅[此处](/cli/azure/hdinsight#az_hdinsight_create-examples)的示例。

## <a name="next-steps"></a>后续步骤

* [适用于 Azure HDInsight 的企业安全性套餐](enterprise-security-package.md)
* [Azure HDInsight 中的企业安全性常规信息和准则](./domain-joined/general-guidelines.md)
