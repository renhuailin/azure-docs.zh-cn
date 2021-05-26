---
title: Azure 机密账本常见问题解答
description: Azure 机密账本常见问题解答
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 1baa1470bcaef8e447b19eb37580b21b94c02df6
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110384926"
---
# <a name="frequently-asked-questions-for-azure-confidential-ledger"></a>Azure 机密账本常见问题解答

## <a name="how-can-i-tell-if-the-acc-ledger-service-would-be-useful-to-my-organization"></a>如何判断 ACC 账本服务是否对组织有用？

机密账本非常适合具有珍贵价值的记录的组织，这些记录价值巨大，足以引诱攻击者尝试破坏基础日志记录/存储系统，其中包括恶意员工可能尝试伪造、修改或删除以前记录的“内部”情况。

## <a name="what-makes-acc-ledger-much-more-secure"></a>什么使 ACC 账本更加安全？

顾名思义，账本利用了 [Azure 机密计算平台](../confidential-computing/index.yml)。 一个账本跨三个或更多相同的实例，每个实例在专用的、经过全面测试的硬件支持的 Enclave 中运行。 账本的完整性通过基于共识的区块链进行维护。

## <a name="when-writing-to-the-acc-ledger-do-i-need-to-store-write-receipts"></a>写入 ACC 账本时，是否需要存储写入回执？

不一定。 目前，某些解决方案要求用户保留写入回执，以便将来进行日志验证。 这要求用户在安全存储设施中管理这些回执，从而增加了额外的负担。 账本通过基于 Merkle 树的方法消除这一难题，其中，写入回执包括指向已签名信任根的完整树路径。 用户可以验证事务，而无需存储或管理任何账本数据。

## <a name="how-do-i-verify-ledgers-authenticity"></a>如何验证账本的真实性？

可以验证客户端正在与之通信的账本服务器节点是否真实。 有关详细信息，请参阅[对机密账本节点进行身份验证](authenticate-ledger-nodes.md)。



## <a name="next-steps"></a>后续步骤

- [Microsoft Azure 机密账本概述](overview.md)