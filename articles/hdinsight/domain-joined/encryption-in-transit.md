---
title: Azure HDInsight 传输中加密
description: 了解安全功能，以便为 Azure HDInsight 群集提供传输中加密。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: ac210adbc29075f27739b08dcf83eeedbeea3ed5
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630356"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>用于 Azure HDInsight 的传输中 IPSec 加密

本文介绍如何为 Azure HDInsight 群集节点之间的通信实现传输中加密。

## <a name="background"></a>背景

Azure HDInsight 提供了多种不同的安全功能来保护企业数据。 这些解决方案按多个要素（外围安全性、身份验证、授权、审核、加密和合规性）分组。 静态数据和传输中的数据都可以应用加密。

Azure 存储帐户上的服务器端加密以及作为 HDInsight 群集一部分的 Azure VM 上的磁盘加密都包括静态加密。

在 HDInsight 上为传输中的数据加密是通过[传输层安全性 (TLS)](../transport-layer-security.md)（用于访问群集网关）和群集节点间的 [Internet 协议安全性 (IPSec)](https://wikipedia.org/wiki/IPsec) 来实现的。 可选择在所有头节点、工作器节点、边缘节点和 zookeeper 节点之间启用 IPSec。 不会为在基于 Windows 的 Vm 和群集中的其他基于 linux 的节点的网关或 [id 代理](./identity-broker.md) 节点之间的流量启用此功能。

## <a name="enable-encryption-in-transit"></a>启用传输中加密

### <a name="azure-portal"></a>Azure 门户

若要使用 Azure 门户来创建启用传输中加密的新群集，请执行以下步骤：

1. 开始正常的群集创建过程。 有关初始的群集创建步骤，请参阅[通过使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集](../hdinsight-hadoop-create-linux-clusters-portal.md)。
1. 完成“基本”和“存储”选项卡上的设置 。 继续进行到“安全性 + 网络”选项卡。

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="创建群集 -“安全性和网络”选项卡。":::

1. 在 " **安全 + 网络** " 选项卡上，选中 " **在传输中启用加密** " 复选框。

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="创建群集 - 启用传输中加密。":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>通过 Azure CLI 创建启用了传输中加密的群集

使用 `isEncryptionInTransitEnabled` 属性启用传输中加密。

可以[下载示例模板和参数文件](https://github.com/Azure-Samples/hdinsight-enterprise-security)。 在使用下面的模板和 Azure CLI 代码片段之前，请将以下占位符替换为其正确值：

| 占位符 | 说明 |
|---|---|
| `<SUBSCRIPTION_ID>` | Azure 订阅的 ID |
| `<RESOURCE_GROUP>` | 要在其中创建新群集和存储帐户的资源组。 |
| `<STORAGEACCOUNTNAME>` | 应该与群集一起使用的现有存储帐户。 名称应采用 `ACCOUNTNAME.blob.core.windows.net` 的形式 |
| `<CLUSTERNAME>` | 你的 HDInsight 群集的名称。 |
| `<PASSWORD>` | 你选择的使用 SSH 及 Ambari 仪表板登录群集的密码。 |
| `<VNET_NAME>` | 将要部署群集的虚拟网络。 |

以下代码片段将会执行下述初始步骤：

1. 登录到 Azure 帐户。
1. 设置要在其中执行创建操作的活动订阅。
1. 为新的部署活动创建新的资源组。
1. 部署模板来创建新群集。

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az group deployment create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>后续步骤

* [Azure HDInsight 中的企业安全性概述](hdinsight-security-overview.md)
* [将 Azure Active Directory 用户同步到 HDInsight 群集](../disk-encryption.md)。
