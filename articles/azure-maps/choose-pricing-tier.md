---
title: 为 Microsoft Azure Maps 选择适当的定价层
description: 了解 Azure Maps 定价层。 了解哪些层提供哪些功能，并查看选择定价层的关键注意事项。
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 98e2e54ee91a398f02da798ef44eeb42c798fbc7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747758"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>在 Azure Maps 中选择适当的定价层

Azure Maps 现提供两个定价层：Gen 1 和 Gen 2。 新的 Gen 2 定价层包含所有 Azure Maps 功能，没有任何 QPS（每秒查询数）限制，允许在 Azure Maps 事务增加时减少成本。 本文旨在帮助你根据需要选择适当的定价层。

## <a name="pricing-tier-targeted-customers"></a>定价层目标客户

请参阅下面的“定价层目标客户”表，更好地了解 Gen 1 和 Gen 2 定价层。  有关详细信息，请参阅 [Azure Maps 定价](https://azure.microsoft.com/pricing/details/azure-maps/)。 如果你目前是 Azure Maps 客户，可以在[此处](how-to-manage-pricing-tier.md)了解如何从 Gen 1 更改为 Gen 2 定价。

| 定价层  | SKU | 目标客户|
|-----------------|----| -----------------|
| Gen 1 | S0          |    S0 定价层适合所有生产阶段的应用程序：从概念验证开发和早期阶段测试到应用程序生产和部署。 但是，此层专为小规模开发和/或并发用户较少的客户而设计。
|        |S1           |    S1 定价层适合采用大规模企业应用程序、任务关键应用程序或具有大量并发用户的客户。 它也适用于那些需要高级地理空间服务的客户。
| Gen 2 | 地图/位置见解 | Gen 2 定价适用于新客户和当前 Azure Maps 客户。 Gen 2 附带一个免费的每月事务层，用于在 Azure 地图上测试和生成。 地图和位置见解 SKU 包含所有 Azure Maps 功能。 此外，没有 QPS（每秒查询数）限制，对于大多数服务，随着 Azure Maps 事务增加，可减少成本。  
|     |  |

## <a name="next-steps"></a>后续步骤

了解有关如何查看和更改定价层的详细信息：

> [!div class="nextstepaction"]
> [管理定价层](how-to-manage-pricing-tier.md)
