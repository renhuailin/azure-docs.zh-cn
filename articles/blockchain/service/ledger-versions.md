---
title: Azure 区块链服务账本版本、修补和升级
description: 概述了 Azure 区块链服务中支持的账本版本。 包括系统修补和升级策略。
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea7c54e229178320329204a3199ab2b4c44058fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "85807734"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>支持的 Azure 区块链服务账本版本

Azure 区块链服务使用基于 Ethereum 的 [Quorum](https://www.goquorum.com/developers) 账本，旨在处理已知参与者组（在 Azure 区块链服务中标识为联盟）内的私人交易。

目前，Azure 区块链服务支持 [Quorum 版本 2.6.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.6.0) 和 [Tessera 事务管理器](https://github.com/jpmorganchase/tessera)。

## <a name="managing-updates-and-upgrades"></a>管理更新和升级

在 Quorum 中，版本控制通过主要版本、次要版本和补丁版本来完成。 例如，如果 Quorum 版本为 2.0.1，则版本类型按如下所示分类：

|主要 | 次要  | 修补程序  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

在 Quorum 发布补丁版本后 30 天内，Azure 区块链服务自动将 Quorum 的补丁版本更新为现有正在运行的成员。

## <a name="availability-of-new-ledger-versions"></a>新账本版本的可用性

在 Quorum 制造商提供后 60 天内，Azure 区块链服务提供 Quorum 账本的最新主要版本和次要版本。 当预配新成员和联盟时，最多提供四个次要版本供联盟选择。 目前不支持升级自/到主要版本或次要版本。 例如，如果正在运行版本 2.x，则目前不支持升级到版本 3.x。 同样，如果正在运行版本 2.2，则目前不支持升级到版本 2.3。

## <a name="how-to-check-quorum-ledger-version"></a>如何检查 Quorum 账本版本

可以通过使用 geth 附加到节点或查看诊断日志来检查 Azure 区块链服务成员上的 Quorum 版本。

### <a name="using-geth"></a>使用 geth

使用 geth 附加到 Azure 区块链服务节点。 例如，`geth attach https://myblockchainmember.blockchain.azure.com:3200/<Access key>`。

在节点连接后，geth 报告 Quorum 版本，如下面的输出所示：

``` text
instance: Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12
```

若要详细了解如何使用 geth，请参阅[快速入门：使用 Geth 附加到 Azure 区块链服务事务节点](connect-geth.md)。

### <a name="using-diagnostic-logs"></a>使用诊断日志

如果启用诊断日志，则会报告事务节点的 Quorum 版本。 例如，下面的节点信息日志消息包括 Quorum 版本。

``` text 
{"NodeName":"transaction-node","Message":"INFO [06-22|05:31:45.156] Starting peer-to-peer node instance=Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12\n"}
{"NodeName":"transaction-node","Message":"[*] Starting Quorum node with QUORUM_VERSION=2.6.0, TESSERA_VERSION=0.10.5 and PRIVATE_CONFIG=/working-dir/c/tm.ipc\n"}
111
```

若要详细了解诊断日志，请参阅[通过 Azure Monitor 监视 Azure 区块链服务](monitor-azure-blockchain-service.md#diagnostic-settings)。

## <a name="how-to-check-genesis-file-content"></a>如何检查 genesis 文件内容

若要检查区块链节点的 genesis 文件内容，可以使用下面的 Ethereum JavaScript API：

``` bash
admin.nodeInfo.protocols
```
可以使用 geth 控制台或 web3 库来调用此 API。 若要详细了解如何使用 geth，请参阅[快速入门：使用 Geth 附加到 Azure 区块链服务事务节点](connect-geth.md)。

## <a name="next-steps"></a>后续步骤

[Azure 区块链服务中的限制](limits.md)
