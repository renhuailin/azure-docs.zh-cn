---
title: 在 Azure Cosmos DB 中优化多区域部署的成本
description: 本文说明如何在 Azure Cosmos DB 中管理多区域部署的成本。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: a559a51feafa310a4645282dc6368f520fc6b972
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459624"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中优化多区域成本
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

可随时对 Azure Cosmos 帐户添加或删除区域。 在与帐户关联的每个区域中会保留为各种 Azure Cosmos 数据库和容器配置的吞吐量。 如果每小时的预配吞吐量（即，跨所有数据库和容器为 Azure Cosmos 帐户配置的 RU/秒总和）为 `T`，并且与数据库帐户关联的 Azure 区域数是 `N`，则对于给定小时，Cosmos 帐户的总预配吞吐量等于 `T x N RU/s`。

具有单个写入区域的预配吞吐量每 100 RU/秒的成本为 0.008 美元/小时，具有多个可写区域的预配吞吐量每 100 RU/秒的成本为 0.016 美元/小时。 若要了解详细信息，请参阅 Azure Cosmos DB [定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。

## <a name="costs-for-multiple-write-regions"></a>多个写入区域的成本

在多区域写入系统中，可用于写入操作的净 RU 会增加 `N` 倍，其中 `N` 是写入区域数。 与单区域写入不同，每个区域现在都可写并且支持冲突解决。 从成本规划的角度来看，若要在 `M` 全球范围内执行 RU/秒的写入，则需要 `RUs` 在容器或数据库级别预配 M。 随后可以添加任意多个区域并将它们用于写入以在全球范围执行 `M` RU 写入。

### <a name="example"></a>示例

请注意，在美国西部为单区域写入配置了一个容器，其中的吞吐量为 10K RU/s，存储了 0.5 TB 的数据。 假设你使用相同的存储和吞吐量添加一个区域 "美国东部"，你希望能够从应用的两个区域写入容器。 新的每月总帐单 (假设每月730小时) 将如下所示：

|**Item**|**使用情况（每月）**|**费率**|**每月成本**|
|----|----|----|----|
|"美国西部" 容器的吞吐量帐单 (单个写入区域)  |10K RU/秒 * 730 小时 |每小时 $0.008 每个 100 RU/秒 |$584 |
|2个区域中的容器的吞吐量帐单-美国西部 & 美国东部 (多个写入区域)  |2 * 10K RU/秒 * 730 小时 |每小时每 100 RU/s 为 0.016 美元 |$2336 |
|美国西部容器的存储帐单 |0.5 TB (或 512 GB)  |0\.25 美元/GB |$128 |
|2个区域中的容器的存储帐单-美国西部 & 美国东部 |2 * 0.5 TB (或 1024 GB)  |0\.25 美元/GB |$256 |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>按每个区域提高吞吐量利用率

如果利用率不足，例如有一个或多个未充分利用的读取区域，则可以采取措施来最大限度地利用读取区域中的 RU，具体方式是从读取区域使用“更改源”或将其移动到另一个次要区域（如果已过度利用）。 你将需要确保先在写入区域中优化预配吞吐量 (RU)。 除非查询非常大，否则写入操作的开销就会大于读取操作的开销，因此维持平稳的利用率可能会很困难。 总的来说，监视区域中已消耗的吞吐量，并根据需要添加或删除区域以缩放读取和写入吞吐量，确保了解对同一区域中部署的任何应用的延迟造成的影响。

## <a name="next-steps"></a>后续步骤

接下来，可通过以下文章详细了解 Azure Cosmos DB 中的成本优化：

* 详细了解[开发和测试优化](optimize-dev-test.md)
* 详细了解[了解 Azure Cosmos DB 帐单](understand-your-bill.md)
* 详细了解如何[优化吞吐量成本](optimize-cost-throughput.md)
* 详细了解如何[优化存储成本](optimize-cost-storage.md)
* 详细了解如何[优化读取和写入成本](optimize-cost-reads-writes.md)
* 详细了解如何[优化查询成本](./optimize-cost-reads-writes.md)
