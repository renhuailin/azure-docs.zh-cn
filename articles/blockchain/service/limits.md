---
title: Azure 区块链服务限制
description: Azure 区块链服务中服务和功能限制的概述
ms.date: 04/02/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 71e1bebf10fa0142870d03977182472da1ad031f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "80676518"
---
# <a name="limits-in-azure-blockchain-service"></a>Azure 区块链服务中的限制

Azure 区块链服务具有服务和功能方面的限制，例如，成员可以具有的节点数、联盟限制和存储量。

## <a name="pricing-tier"></a>定价层

事务和验证器节点的最大数量限制取决于是在基本定价层还是在标准定价层预配 Azure 区块链服务。

| 定价层 | 事务节点数最大值 | 验证器节点数最大值 |
|:---|:---:|:---:|
| 基本 | 10 | 1 |
| Standard | 10 | 2 |

联盟网络应该至少有两个 Azure 区块链服务标准层节点。 标准层节点包括两个验证器节点。 需要有四个验证器节点才能满足[伊斯坦布尔拜占庭容错共识](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus)。

基本层用于开发、测试和概念证明。 标准层用于生产级部署。 如果使用区块链数据管理器或发送大量专用事务，还应使用“标准”层。

不支持在创建成员后在“基本”定价层和“标准”定价层之间进行切换。

## <a name="storage-capacity"></a>存储容量

每个节点可用于账本数据和日志的最大存储量为 1.8 TB。

不支持减小账本和日志存储大小。
## <a name="consortium-limits"></a>联盟限制

* 联盟和成员名称必须独一无二，有别于 Azure 区块链服务中的其他联盟和成员名称。

* 无法更改成员和联盟名称

* 联盟中的所有成员都必须在同一定价层中

* 所有参与联盟的成员都必须在同一区域中

    在联盟中创建的第一个成员决定了区域。 该联盟的受邀成员必须与第一个成员位于同一区域。 将所有成员限制在同一区域有助于确保网络共识免受负面影响。

* 联盟必须至少有一名管理员

    如果联盟中只有一名管理员，则该管理员无法将自己从联盟中删除，也无法删除其成员，直到联盟中添加或提升了另一名管理员。

* 无法再次添加从联盟删除的成员

    必须重新邀请他们加入该联盟并创建新成员。 他们现有的成员资源不会被删除，以便保留历史事务。

* 联盟中的所有成员都必须使用同一账本版本

    若要详细了解 Azure 区块链服务中可用的修补、更新和账本版本，请参阅[修补、更新和版本](ledger-versions.md)。

## <a name="performance"></a>性能

请勿为每个事务提交使用 eth.estimate gas 函数。 eth.estimate 函数会大量消耗内存。 多次调用该函数会大幅减少每秒事务数。

如果可能，请使用保守的 gas 值来提交事务，并最大程度地减少 eth.estimate 的使用。

## <a name="next-steps"></a>后续步骤

详细了解有关系统修补和升级的策略 - [修补、更新和版本](ledger-versions.md)。
