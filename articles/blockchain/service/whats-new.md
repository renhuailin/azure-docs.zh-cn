---
title: 新增功能 发行说明 - Azure 区块链服务
description: 了解 Azure 区块链服务的新增功能，例如最新发行说明、版本、已知问题和即将应用的更改。
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea44366dd88ae1b98865727c9a3d3e038c924120
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "122652963"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Azure 区块链服务有哪些新增功能？

> 将此 URL：`https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` 复制并粘贴到 RSS 源阅读器[![ RSS 源阅读器图标](./media/whats-new/feed-icon-16x16.png)](/api/search/rss?locale=en-us&search=%2522Release%2bnotes%2b-%2bAzure%2bBlockchain%2bService%2522)，获取有关何时重新访问此页以获得更新的通知。

Azure 区块链服务会不断改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 新功能
- 版本升级
- 已知问题

---

## <a name="may-2021"></a>2021 年 5 月

[!INCLUDE [Retirement note](./includes/retirement.md)]

## <a name="june-2020"></a>2020 年 6 月

### <a name="version-upgrades"></a>版本升级

- Quorum 版本升级到 2.6.0。 版本 2.6.0 允许发送签名的专用事务。 有关发送专用事务的详细信息，请参阅 [Quorum API 文档](https://docs.goquorum.consensys.net/en/latest/Reference/APIs/ContractExtensionAPIs/#apis)。
- Tessera 版本升级到 0.10.5。

### <a name="contract-size-and-transaction-size-increased-to-128-kb"></a>合同大小和事务大小增加到 128 KB

类型：配置更改

合同大小 (MaxCodeSize) 已增加到 128 KB，以便部署更大的智能协定。 此外，事务大小 (txnSizeLimit) 已增加到 128 KB。 配置更改适用于 2020 年 6 月 19日之后在 Azure 区块链服务上创建的新联盟。

### <a name="trietimeout-value-reduced"></a>TrieTimeout 值减少

类型：配置更改

TrieTimeout 值减少，以便更频繁地将内存状态写入磁盘。 较低值能保证在节点崩溃的罕见情况下能更快恢复节点。

## <a name="may-2020"></a>2020 年 5 月

### <a name="version-upgrades"></a>版本升级

- Ubuntu 版本升级到 18.04
- Quorum 版本升级到 2.5.0
- Tessera 版本升级到 0.10.4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Azure 区块链服务支持发送 rawPrivate 事务

类型：功能

客户可以对节点上的帐户之外的专用事务进行签名。

### <a name="two-phase-member-provisioning"></a>两阶段成员预配

类型：增强

两个阶段有助于优化在长期存在的联盟中创建成员的方案。 第一阶段预配成员基础结构。 在第二阶段中，成员与区块链同步。 两阶段预配有助于避免由于超时导致的成员创建失败。

## <a name="known-issues"></a>已知问题

### <a name="ethestimategas-function-throws-exception-in-quorum-v260"></a>estimateGas 函数在 Quorum v2.6.0 中引发异常

在 Quorum v2.6.0 中，调用 estimateGas 函数而不提供附加值参数会导致方法处理程序崩溃异常。 Quorum 团队已收到通知，修复任务在 2020 年 7 月完成。 修复完成前可以使用以下解决方法：

- 避免使用 estimateGas，因为它会影响性能。 有关 estimateGas 性能问题的详细信息，请参阅[调用 estimateGas 函数可降低性能](#calling-ethestimategas-function-reduces-performance)。 为每项事务添加天然气值。 如果未提供导致 Quorum v 2.6.0 崩溃的天然气值，大多数库将调用 estimateGas。
- 如果需要调用 estimateGas，Quorum 团队建议将附加参数值作为0传递，以作为解决方法。

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>如果验证器节点少于四个，则挖掘停止

生产网络应至少具有四个验证器节点。 Quorum 建议至少需要四个验证器节点，以满足 IBFT 崩溃容错 (3F+1)。 应该至少具备两个 Azure 区块链服务“标准”层节点，才能获取四个验证器节点。 使用两个验证器节点预配标准节点。

如果 Azure 区块链服务上的区块链网络没有四个验证器节点，那么挖掘可能会在网络上停止。 可以通过对已处理的块设置警报来检测已停止的挖掘。 在运行状况良好的网络中，每个节点每五分钟可处理 60 块。

作为一种缓解措施，Azure 区块链服务团队必须重新启动该节点。 客户需要打开支持请求才能重新启动该节点。 Azure 区块链服务团队正在努力实现自动检测和修复挖掘问题。

使用“标准”层进行生产级部署。 使用“基本”层进行开发、测试和概念证明。 不支持在创建成员后在“基本”定价层和“标准”定价层之间进行切换。

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>区块链数据管理器需要标准层节点

如果使用区块链数据管理器，那么也要使用“标准”层。 “基本”层仅有 4 GB 内存。 因此，该层无法扩展到区块链数据管理器及其上运行的其他服务所需的使用量。

使用“基本”层进行开发、测试和概念证明。 不支持在创建成员后在“基本”定价层和“标准”定价层之间进行切换。

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>大量解锁帐户调用导致 geth 崩溃

提交事务时，大量解锁帐户调用会导致 geth 在事务节点上崩溃。 因此，必须停止引入事务。 否则，挂起事务队列将增加。

Geth 会在不到一分钟的时间内自动重启。 同步可能需要较长时间，具体时长取决于节点。 Azure 区块链服务团队正在启用一项可以减少同步时间的存档功能。

若要识别 geth 崩溃，可以在应用程序日志的区块链消息中检查日志中是否有任何错误消息。 还可以检查处理块在挂起事务增加时是否减少。

若要缓解此问题，请发送签名事务而非发送带有解锁账户命令的无签名事务。 对于已进行外部签名的事务，无需解锁帐户。

如果要发送未签名的事务，请将 0 作为解锁命令的时间参数发送，从而将该帐户无限时解锁。 提交所有事务后，可以再次将该帐户锁定。

下面是 Azure 区块链服务使用的 geth 参数。 不能调整这些参数。

- 伊斯坦布尔块周期：5秒
- 最低天然气限制：700000000
- 最高天然气限制：800000000

### <a name="large-volume-of-private-transactions-reduces-performance"></a>大量专用事务会降低性能

如果使用 Azure 区块连基本层和专用事务，那么 Tessera 可能会崩溃。

可以通过查看区块链应用程序日志和搜索消息来检测 Tessera 崩溃`Tessera crashed. Restarting Tessera...`。

发生崩溃时，Azure 区块链服务会重启 Tessera。 重启大约需要一分钟时间。

使用“标准”层发送大量专用事务。 使用“基本”层进行开发、测试和概念证明。 不支持在创建成员后在“基本”定价层和“标准”定价层之间进行切换。

### <a name="calling-ethestimategas-function-reduces-performance"></a>调用 eth.estimateGas 函数会降低性能

多次调用 estimateGas 函数可大幅减少每秒事务数。 不要对每个提交事务使用 estimateGas 函数。 eth.estimateGas 函数消耗大量内存。

如果可能，请使用保守天然气值提交事务，并将 estimateGas 的使用降至最低。

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>智能合同中的无限循环会降低性能

避免在智能合同中出现无限循环，因为无限循环会降低性能。 有关更多信息，请参见以下资源：

- [避免无限循环](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [智能协定安全最佳做法](https://github.com/ConsenSys/smart-contract-best-practices)
- [Quorum 提供的智能合同准则](https://docs.goquorum.consensys.net/en/stable/Concepts/Security/Framework/DecentralizedApplication/SmartContractsSecurity/)
- [Solidity 提供的天然气限制和循环准则](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)