---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/21/2021
ms.author: sngun
ms.openlocfilehash: cebf848f673b9e2651e6598d64aefff0cd371553
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2021
ms.locfileid: "108064698"
---
## <a name="capacity-planner-modes"></a>容量规划器模式


|**模式**  |**说明**  |
|---------|---------|
|基本|提供快速、大概的 RU/秒和成本估算。 此模式为索引编制策略、一致性和其他参数采用默认的 Azure Cosmos DB 设置。 <br/><br/>当评估要在 Azure Cosmos DB 上运行的潜在工作负荷时，可以使用基本模式进行快速、大概的估算。 若要了解详细信息，请参阅如何[使用基本模式估算成本](#basic-mode)。|
|高级|提供更详细的 RU/秒和成本估算，还可以调整其他设置（索引编制策略、一致性级别以及影响成本和吞吐量的其他参数）。 <br/><br/>当为新项目估算 RU/秒或需要更详细的估算时，请使用高级模式。 若要了解详细信息，请参阅如何[使用高级模式估算成本](#advanced-mode)。|
