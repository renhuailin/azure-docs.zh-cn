---
title: Azure HDInsight 虚拟网络体系结构
description: 了解在 Azure 虚拟网络中创建 HDInsight 群集时可用的资源。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 511f1b1bba227e58303a47994d7a6f7bd8b14604
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290982"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight 虚拟网络体系结构

本文介绍在将 HDInsight 群集部署到自定义的 Azure 虚拟网络中时提供的资源。 此信息可帮助你将本地资源连接到 Azure 中的 HDInsight 群集。 有关 Azure 虚拟网络的详细信息，请参阅[什么是 Azure 虚拟网络？](../virtual-network/virtual-networks-overview.md)。

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Azure HDInsight 群集中的资源类型

Azure HDInsight 群集包含不同类型的虚拟机（或节点）。 每个节点类型对于系统的正常运行发挥着相应的作用。 下表汇总了这些节点类型及其在群集中的作用。

| 类型 | 说明 |
| --- | --- |
| 头节点 |  对于除 Apache Storm 以外的所有群集类型，头节点托管用于管理分布式应用程序的执行的进程。 头节点也是可以通过 SSH 连接到其中并执行应用程序的节点。连接后，系统会协调这些应用程序，使其可在不同的群集资源上运行。 所有群集类型的头节点数目固定为 2 个。 |
| ZooKeeper 节点 | ZooKeeper 协调执行数据处理的节点之间的任务。 它还执行头节点的主控选择，并跟踪哪个头节点正在运行特定的主服务。 ZooKeeper 节点数目固定为 3 个。 |
| 工作器节点 | 表示支持数据处理功能的节点。 可以在群集中添加或删除工作器节点，以缩放计算能力和控制成本。 |
| 区域节点 | 对于 HBase 群集类型，区域节点（也称为数据节点）将运行区域服务器。 区域服务器为 HBase 管理的一部分数据提供服务，并对其进行管理。 可以在群集中添加或删除区域节点，以缩放计算能力和控制成本。|
| Nimbus 节点 | 对于 Storm 群集类型，Nimbus 节点提供类似于头节点的功能。 Nimbus 节点通过 Zookeeper 将任务分配给群集中的其他节点，Zookeeper 协调 Storm 拓扑的运行。 |
| 监督器节点 | 对于 Storm 群集类型，监督器节点执行 Nimbus 节点所提供的指令以进行处理。 |

## <a name="resource-naming-conventions"></a>资源命名约定

在对群集中的节点进行寻址时，使用完全限定的域名 (FQDN)。 可以使用 [Ambari API](hdinsight-hadoop-manage-ambari-rest-api.md) 获取群集中各种节点类型的 FQDN。

这些 FQDN 的格式为 `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net`。

对于头节点，`<node-type-prefix>` 为“hn”，对于工作节点为“wn”，对于 zookeeper 节点为“zn”。

如果只需要主机名，则仅使用 FQDN 的第一部分：`<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>基本虚拟网络资源

下图显示了 HDInsight 节点和网络资源在 Azure 中的位置。

:::image type="content" source="./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png" alt-text="在 Azure 自定义 VNET 中创建的 HDInsight 实体示意图" border="false":::

Azure 虚拟网络中的默认资源包括上表中提到的群集节点类型。 同时包括支持虚拟网络和外部网络之间的通信的网络设备。

下表汇总了将 HDInsight 部署到自定义 Azure 虚拟网络时创建的 9 个群集节点。

| 资源类型 | 提供的数量 | 详细信息 |
| --- | --- | --- |
|头节点 | two |    |
|Zookeeper 节点 | three | |
|工作器节点 | two | 此数字根据群集的配置和规模而异。 Apache Kafka 至少需要 3 个工作器节点。  |
|网关节点 | two | 网关节点是在 Azure 中创建的、但不会在订阅中显示的 Azure 虚拟机。 如果需要重新启动这些节点，请联系支持人员。 |

在与 HDInsight 配合使用的虚拟网络中，会自动创建以下网络资源：

| 网络资源 | 提供的数量 | 详细信息 |
| --- | --- | --- |
|负载均衡器 | three | |
|网络接口 | 9 个 | 此值基于普通群集，在此类群集中，每个节点具有自身的网络接口。 9 个接口分别用于上表中所述的 2 个头节点、3 个 ZooKeeper 节点、2 个工作器节点和 2 个网关节点。 |
|公共 IP 地址 | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>用于连接 HDInsight 的终结点

可通过三种方式访问 HDInsight 群集：

- 虚拟网络 (`CLUSTERNAME.azurehdinsight.net`) 外部的 HTTPS 终结点。
- 直接连接到位于 `CLUSTERNAME-ssh.azurehdinsight.net` 中的头节点的 SSH 终结点。
- 虚拟网络 (`CLUSTERNAME-int.azurehdinsight.net`) 内部的 HTTPS 终结点。 请注意此 URL 中的“`-int`”。 此终结点将解析为该虚拟网络中的专用 IP，无法从公共 Internet 访问。

在这 3 个终结点中，每个终结点分配有一个负载均衡器。

此外，将为 2 个终结点提供公共 IP 地址，以便从虚拟网络外部进行连接。

1. 将为负载均衡器分配 1 个公共 IP，以便从 Internet `CLUSTERNAME.azurehdinsight.net` 连接到群集时使用完全限定的域名 (FQDN)。
1. 第二个公共 IP 地址用于仅限 SSH 的域名 `CLUSTERNAME-ssh.azurehdinsight.net`。

## <a name="next-steps"></a>后续步骤

- [使用专用终结点保护传入虚拟网络中 HDInsight 群集的流量](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
