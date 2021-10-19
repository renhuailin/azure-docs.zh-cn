---
title: 在受限 HDInsight 群集上启用专用链接（预览版）
description: 了解如何使用 Azure 专用链接连接外部 HDInsight 群集。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 9f432d37e58069decdc9a97e55d926aed3b7277f
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710012"
---
# <a name="enable-private-link-on-hdinsight-cluster-preview"></a>在 HDInsight 群集上启用专用链接（预览版）

## <a name="overview"></a>概述
本文介绍如何利用 Azure 专用链接通过 Microsoft 主干网络跨网络以专用方式连接到 HDInsight 群集。 本文是我们的重要文章[在 Azure HDInsight 中限制群集连接](./hdinsight-restrict-public-connectivity.md)的扩充，前文重点介绍了如何限制公共连接。 如果你可能选择在 HDInsight 群集和相关资源之间建立公共连接，请考虑遵循[在 Azure HDInsight 中控制网络流量](./control-network-traffic.md)中的指南来限制群集的连接

在 VNet 对等互连不可用（或未启用）的跨 VNet 方案中，可以利用专用链接。 例如，如果要将 Azure 数据工厂与 Azure HDInsight 集成，出于合规性和安全性原因，Azure 数据工厂需要通过专用网络（即专用链接）连接到 HDInsight 群集。

> [!NOTE]
> 限制公共连接是启用专用链接的先决条件，不应被视为相同的功能。

专用链接是一项可选功能，默认情况下处于禁用状态。 该功能仅在 `resourceProviderConnection` 网络属性设置为 outbound 时可用，如[在 Azure HDInsight 中限制群集连接](./hdinsight-restrict-public-connectivity.md)一文中所述。

当 `privateLink` 设置为“*启用*”时，会创建内部 [标准负均衡器](../load-balancer/load-balancer-overview.md) (SLB) ，并为每个 SLB 预配 Azure 专用链接服务。 专用链接服务可让你从专用终结点访问 HDInsight 群集。

## <a name="prerequisites"></a>先决条件

标准负载均衡器不会像基本负载均衡器那样自动提供[公共出站 NAT](../load-balancer/load-balancer-outbound-connections.md)。 要连接到出站公共 HDInsight 依赖项，必须提供自己的 NAT 解决方案，例如 NAT 网关或[防火墙](./hdinsight-restrict-outbound-traffic.md)提供的 NAT。 你的 HDInsight 群集仍需要其出站依赖项的访问权限。 如果不允许这些出站依赖项，则群集创建可能会失败。 还必须在子网上配置网络安全组才能启用出站连接。

### <a name="1--configure-a-default-network-security-group-nsg-on-the-subnet"></a>1.  在子网上配置默认的网络安全组 (NSG)

在要部署 HDInsight 群集的子网上创建并添加网络安全组。

### <a name="2--disable-network-policies-for-private-link-service"></a>2.  对专用链接服务禁用网络策略

若要成功创建专用链接服务，必须显式[禁用专用链接服务的网络策略](../private-link/disable-private-link-service-network-policy.md)。

### <a name="3--configure-a-nat-gateway-on-the-subnet"></a>3.  在子网上配置 NAT 网关

如果不想为 NAT 配置防火墙或网络虚拟设备 (NVA)，可以选择使用 NAT 网关，否则请跳至下一个先决条件。

首先，只需将 NAT 网关（在虚拟网络中使用新的公共 IP 地址）添加到虚拟网络的已配置子网中。 当流量需要超出虚拟网络的范围时，此网关负责将专用内部 IP 地址转换为公共地址。

### <a name="4--using-firewall-or-network-virtual-appliance-nvas-for-nat-optional"></a>4.  对 NAT 使用防火墙或网络虚拟设备 (NVA)（可选）
若要开始基本设置，请首先将新子网“AzureFirewallSubnet”添加到虚拟网络中。 创建后，使用此子网配置新防火墙并添加防火墙策略。 设置防火墙后，使用此防火墙的专用 IP 作为路由表中的 `nextHopIpAddress`。 将此路由表添加到虚拟网络的已配置子网中。
有关设置防火墙的其他详细信息，请参阅[在 Azure HDInsight 中控制网络流量](./control-network-traffic.md)

下图显示了创建群集之前所需的网络配置的示例。 在此示例中，将使用 UDR 将所有出站流量强制发送到 Azure 防火墙，并且在创建群集之前，所需的出站依赖项在防火墙上应设置为“允许”。 对于企业安全性套餐群集，可通过 VNet 对等互连提供与 Azure Active Directory 域服务的网络连接。

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="群集创建前的专用链接环境示意图":::

## <a name="manage-private-endpoints-for-azure-hdinsight"></a>管理 Azure HDInsight 的专用终结点

可以对 Azure HDInsight 群集使用[专用终结点](../private-link/private-endpoint-overview.md)，以允许虚拟网络 (VNet) 上的客户端通过[专用链接](../private-link/private-link-overview.md)安全地访问你的群集。 VNet 上的客户端与 HDInsight 群集之间的网络流量通过 Microsoft 主干网络遍历，从而避免了公共 Internet 的暴露。

:::image type="content" source="media/hdinsight-private-link/private-endpoint-experience.png" alt-text="专用终结点管理体验示意图":::

私有链接服务使用者（例如 Azure 数据工厂）可以选择两种连接审批方法：
* 自动：如果服务使用者对 HDInsight 资源具有 Azure RBAC 权限，则使用者可以选择自动审批方法。 在这种情况下，当请求到达 HDInsight 资源时，HDInsight 不需要执行任何操作，并且将自动批准连接。
* **手动**：另一方面，如果 HDInsight 对服务提供商资源没有 Azure RBAC 权限，则使用者可以选择手动审批方法。 在这种情况下，连接请求会以挂起状态出现在 HDInsight 资源上。 需要先由 HDInsight 资源手动批准请求，然后才能建立连接。 

若要管理专用终结点，请在 Azure 门户的群集视图中，导航到“安全 + 网络”下的“网络(预览)”部分。 可以在这里看到所有现有连接、连接状态以及专用终结点详细信息。
还可以批准、拒绝或删除现有连接。 创建专用连接时，可以指定还要连接到哪些 HDInsight 资源（网关、头节点等 ）。

下表显示了各种 HDInsight 资源操作以及专用终结点对应的连接状态。 HDInsight 资源还可以稍后更改专用终结点连接的连接状态，而无需使用者进行干预。 此操作将更新使用者端的终结点状态。

| 服务提供商操作 | 服务使用者专用终结点状态 | 说明 |
| --------- | --------- | --------- |
| 无 | 挂起的 | 连接是手动创建的，正在等待专用链接资源所有者批准。 |
| 审批 | 已批准 | 连接已自动或手动批准，随时可供使用。 |
| 拒绝 | 已拒绝 | 连接已被专用链接资源所有者拒绝。 |
| 删除 | 已断开连接 | 连接已被专用链接资源所有者删除；专用终结点仅供参考，应将其删除以清理资源。 |

## <a name="configure-dns-to-connect-over-private-endpoints"></a>配置 DNS 以通过专用终结点进行连接

如下图所示，设置网络后，可以创建启用了出站资源提供程序连接和专用链接的群集。
要访问专用群集，可以使用专用链接 DNS 扩展和专用终结点。 当 `privateLink` 设置为“enabled”时，你可以创建自己的专用终结点，并可通过专用 DNS 区域配置 DNS 解析。
在 Azure 托管的公共 DNS 区域 `azurehdinsight.net` 中创建的专用链接条目如下所示：

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```
下图显示了一个专用 DNS 条目的示例，该条目配置为允许从非对等互连的虚拟网络或者无法直接看到群集的虚拟网络访问群集。 可以使用 Azure 专用区域来覆盖 `*.privatelink.azurehdinsight.net` FQDN 并解析客户端网络中的专用终结点 IP 地址。
这仅对 `<clustername>.azurehdinsight.net` 显示，但也会扩展到其他群集终结点。

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="专用链接体系结构示意图":::

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

* [适用于 Azure HDInsight 的企业安全性套餐](enterprise-security-package.md)
* [Azure HDInsight 中的企业安全性常规信息和准则](./domain-joined/general-guidelines.md)