---
title: 基本层预览版 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL - 超大规模 (Citus) 的单节点基本层
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c8cad5eb0983715a7cc7c18249243e93a2c498d7
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023836"
---
# <a name="basic-tier-preview"></a>基本层（预览版）

> [!IMPORTANT]
> 超大规模 (Citus) 基本层目前以预览版提供。  此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
>
> 请参阅[超大规模 (Citus) 的预览功能](hyperscale-preview-features.md)，以查看其他新功能的完整列表。

Azure Database for PostgreSQL - 超大规模 (Citus) 中的基本层是创建可在以后进行缩放的小型服务器组的简单方法。 标准层中的服务器组有一个协调器节点和至少两个工作器节点，而基本层在单一数据库节点中运行所有内容。

除了使用的节点更少外，基本层还涵盖标准层的所有功能。 与标准层一样，它支持高可用性、只读副本和列式表存储，以及其他功能。

## <a name="choosing-basic-vs-standard-tier"></a>选择基本层还是标准层

对于初始开发、测试和持续集成，基本层是一种经济而方便的部署选项。 它使用单一数据库节点并显示与标准层相同的 SQL API。 你可以使用基本层测试应用程序，然后再[升级到标准层](howto-hyperscale-scale-grow.md#add-worker-nodes)，并确信接口保持不变。

基本层也适用于生产中较小的工作负载（一旦它从预览版变为正式版）。 通过增加服务器 vCore 的数量，可在基本层内垂直缩放空间。

如果需要更大的规模，请使用标准层。 它允许的最小服务器组有一个协调器节点和两个工作器节点。 你可以根据自己的用例选择使用更多节点，如[初始大小调整](howto-hyperscale-scale-initial.md)操作指南中所述。

## <a name="next-steps"></a>后续步骤

* 了解如何[预配基本层](quickstart-create-hyperscale-basic-tier.md)
* 准备就绪后，请参阅如何从基本层[升级](howto-hyperscale-scale-grow.md#add-worker-nodes)到标准层
* 基本层和标准层中均提供[列式存储](concepts-hyperscale-columnar.md)选项
