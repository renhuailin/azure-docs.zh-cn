---
title: 通过专用链接 (预览) 保护和隔离 Azure HDInsight 群集
description: 了解如何使用 Azure Private Link 隔离虚拟网络中的 Azure HDInsight 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 66c9a3afb91aaff448d6eadc86175d8515be766c
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94889076"
---
# <a name="secure-and-isolate-azure-hdinsight-clusters-with-private-link-preview"></a>通过专用链接 (预览) 保护和隔离 Azure HDInsight 群集

在 Azure HDInsight 的 [默认虚拟网络体系结构](./hdinsight-virtual-network-architecture.md)中，HDInsight 资源提供程序 (RP) 使用公共 IP 地址与群集进行通信。 某些方案需要完全的网络隔离，无需使用公共 IP 地址。 本文介绍可用于创建专用 HDInsight 群集的高级控件。 若要了解如何在不完全隔离网络的情况下限制进出群集的流量，请参阅 [在 Azure HDInsight 中控制网络流量](./control-network-traffic.md)。

可以通过配置 Azure 资源管理器 (ARM) 模板中的特定网络属性来创建专用 HDInsight 群集。 你可以使用两个属性来创建专用 HDInsight 群集：

* 通过将设置 `resourceProviderConnection` 为出站来删除公共 IP 地址。
* 启用 Azure 专用链接，并通过将设置为启用来使用 [专用终结点](../private-link/private-endpoint-overview.md) `privateLink` 。

## <a name="remove-public-ip-addresses"></a>删除公共 IP 地址

默认情况下，HDInsight RP 使用公共 Ip 向群集进行 *入站* 连接。 如果 `resourceProviderConnection` 网络属性设置为 " *出站*"，则它会反转到 HDInsight RP 的连接，以便始终从群集内部向 RP 发起连接。 如果没有入站连接，则不需要入站服务标记或公共 IP 地址。

默认虚拟网络体系结构中使用的基本负载均衡器会自动提供公共 NAT (网络地址转换) 来访问所需的出站依赖项，如 HDInsight RP。 如果要限制到公共 internet 的出站连接，可以 [配置防火墙](./hdinsight-restrict-outbound-traffic.md)，但这不是必需的。

配置 `resourceProviderConnection` 到出站还允许使用专用终结点访问特定于群集的资源，例如 Azure Data Lake Storage Gen2 或外部元存储。 使用这些资源的专用终结点并不 mandetory，但如果你计划为这些资源提供专用终结点，则必须配置 `before` 你创建 HDInsight 群集所需的专用终结点和 DNS 条目。 建议在创建群集时创建并提供所需的所有外部 SQL 数据库，例如 Apache Ranger、Ambari、Oozie 和 Hive 元存储。 需要的是，所有这些资源都必须可通过其自己的专用终结点从群集子网中进行访问，否则必须是可访问的。

不支持将专用终结点用于 Azure Key Vault。 如果使用 Azure Key Vault 进行静态 CMK 加密，则必须可从无专用终结点的 HDInsight 子网中访问 Azure Key Vault 终结点。

下图显示了在设置为 "出站" 时可能的 HDInsight 虚拟网络体系结构可能如下所示 `resourceProviderConnection` ：

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="使用出站资源提供程序连接的 HDInsight 体系结构示意图":::

创建群集后，应设置正确的 DNS 解析。 以下规范名称 DNS 记录 (在 Azure 托管的公共 DNS 区域上创建的 CNAME) ： `azurehdinsight.net` 。

```dns
<clustername>    CNAME    <clustername>-int
```

若要使用群集 Fqdn 访问群集，可以直接使用内部负载均衡器专用 Ip，或根据需要使用自己的专用 DNS 区域替代群集终结点。 例如，可以有一个专用 DNS 区域 `azurehdinsight.net` 。 并根据需要添加专用 Ip：

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

## <a name="enable-private-link"></a>启用专用链接

默认情况下禁用的 "专用链接" 需要广泛的网络知识，以便在创建群集之前正确设置用户定义的路由 (UDR) 和防火墙规则。 使用此设置是可选的，但仅当 `resourceProviderConnection` network 属性设置为 " *出站* " 时才可用，如前一部分中所述。

如果 `privateLink` 设置为 " *启用*"，则会创建内部 [标准负载平衡](../load-balancer/load-balancer-overview.md) 器 (slb) ，并为每个 SLB 预配 Azure 专用链接服务。 专用链接服务可让你从专用终结点访问 HDInsight 群集。

标准负载均衡器不会自动提供 [公共出站 NAT](../load-balancer/load-balancer-outbound-connections.md) （如基本负载均衡器）。 对于出站依赖项，你必须提供自己的 NAT 解决方案，如 [虚拟网络 NAT](../virtual-network/nat-overview.md) 或 [防火墙](./hdinsight-restrict-outbound-traffic.md)。 你的 HDInsight 群集仍需要访问其出站依赖项。 如果不允许这些出站依赖项，则群集创建可能会失败。

### <a name="prepare-your-environment"></a>准备环境

对于 successgfull 创建专用链接服务，必须显式 [禁用专用链接服务的网络策略](../private-link/disable-private-link-service-network-policy.md)。

下图显示了创建群集之前所需的网络配置的示例。 在此示例中，在创建群集之前，将使用 UDR 将所有出站流量 [强制](../firewall/forced-tunneling.md) 发送到 Azure 防火墙，并将所需的出站依赖关系 "允许" 到防火墙上。 对于企业安全性套餐群集，可通过 VNet 对等互连提供与 Azure Active Directory 域服务的网络连接。

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="创建群集之前的专用链接环境示意图":::

设置网络后，可以创建具有出站资源提供程序连接和启用了专用链接的群集，如下图所示。 在此配置中，没有为每个标准负载均衡器预配公共 Ip 和专用链接服务。

:::image type="content" source="media/hdinsight-private-link/after-cluster-creation.png" alt-text="群集创建后的专用链接环境示意图":::

### <a name="access-a-private-cluster"></a>访问专用群集

若要访问专用群集，可以直接使用内部负载均衡器专用 Ip，也可以使用专用链接 DNS 扩展和专用终结点。 如果设置为 " `privateLink` 已启用"，则可以创建自己的专用终结点，并通过专用 dns 区域配置 DNS 解析。

在 Azure 托管的公共 DNS 区域中创建的专用链接条目 `azurehdinsight.net` 如下所示：

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```

下图显示了从虚拟网络访问群集所需的专用 DNS 条目的示例，该网络不对等互连，或者无法直接访问群集负载平衡器。 你可以使用 Azure 专用区域替代 `*.privatelink.azurehdinsight.net` fqdn 并解析为你自己的专用终结点 IP 地址。

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="专用链接体系结构示意图":::

## <a name="how-to-create-clusters"></a>如何创建群集？
### <a name="use-arm-template-properties"></a>使用 ARM 模板属性

下面的 JSON 代码段包含需要在 ARM 模板中配置以创建专用 HDInsight 群集的两个网络属性。

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled" | "Disabled"
}
```

有关包含许多 HDInsight 企业安全功能（包括私有链接）的完整模板，请参阅 [hdinsight 企业安全模板](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)。

### <a name="use-azure-powershell"></a>使用 Azure PowerShell

若要使用 powershell，请参阅 [此处](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster?view=azps-5.1.0#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)的示例。

### <a name="use-azure-cli"></a>使用 Azure CLI
若要使用 Azure CLI，请参阅 [此处](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az_hdinsight_create-examples)的示例。

## <a name="next-steps"></a>后续步骤

* [适用于 Azure HDInsight 的企业安全性套餐](enterprise-security-package.md)
* [Azure HDInsight 中的企业安全性常规信息和准则](./domain-joined/general-guidelines.md)
