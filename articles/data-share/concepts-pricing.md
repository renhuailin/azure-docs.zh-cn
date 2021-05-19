---
title: 了解 Azure Data Share 定价
description: 了解 Azure Data Share 的定价方式
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 1c2c58e206a70a3801c712df3b5582409712d3c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "88136756"
---
# <a name="understand-azure-data-share-pricing"></a>了解 Azure Data Share 定价

对于基于快照的共享，Azure Data Share 会针对数据集快照和快照执行进行收费。 本文介绍如何借助快照历史记录信息对数据集快照和快照执行进行估算。 目前，数据提供程序对数据集快照和快照执行计费。

## <a name="dataset-snapshot"></a>数据集快照

数据集快照是一种将数据集从其源移动到目标的操作。 为共享创建快照时，共享中每个数据集的快照都是一项数据集快照操作。 按照以下步骤查看数据集快照的列表。 

1. 在 Azure 门户中，导航到 Data Share 资源。

1. 从“已发送共享”或“已接收共享”中选择共享。

1. 单击“历史记录”选项卡。

1. 单击快照的“开始时间”。
 
    ![快照历史记录](./media/concepts/concepts-pricing/pricing-snapshot-history.png "快照历史记录") 

1. 查看数据集快照操作的列表。 每个行项对应一项数据集快照操作。 在此示例中，有两个数据集快照。

    ![数据集快照操作](./media/concepts/concepts-pricing/pricing-dataset-snapshot.png "数据集快照操作")

## <a name="snapshot-execution"></a>快照执行

快照执行包括将数据集从源移动到目标所需的资源。 对于每个数据集快照操作，快照执行的计算方式为 vCore 数乘以快照持续时间。 费用按分钟计算，并向上舍入。 基于源目标对和数据模式动态选择 vCore 的数目。 按照以下步骤查看用于数据集快照操作的快照开始时间、结束时间和 vCore 数。

1. 在 Azure 门户中，导航到 Data Share 资源。

1. 从“已发送共享”或“已接收共享”中选择共享。

1. 单击“历史记录”选项卡。

1. 单击快照的“开始时间”。

    ![快照历史记录](./media/concepts/concepts-pricing/pricing-snapshot-history.png "快照历史记录") 

1. 单击数据集快照操作的“状态”。

    ![数据集快照状态](./media/concepts/concepts-pricing/pricing-snapshot-status.png "数据集快照状态")

1. 查看用于此数据集快照操作的开始时间、结束时间和 vCore 数。 

    ![快照执行](./media/concepts/concepts-pricing/pricing-snapshot-execution.png "快照执行")

## <a name="next-steps"></a>后续步骤

- 获取最新定价信息 - [Azure Data Share 定价](https://azure.microsoft.com/pricing/details/data-share/)
- 使用 Azure 定价计算器估算成本 - [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)
