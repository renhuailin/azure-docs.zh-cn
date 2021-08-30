---
title: 扫描和引入
description: 本文介绍了 Azure Purview 中的扫描和引入。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 140e87f4e03081825fe75ba73b7c5ebd33b20ada
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122398008"
---
# <a name="scans-and-ingestion-in-azure-purview"></a>Azure Purview 中的扫描和引入

本文概述了 Azure Purview 中的扫描和引入功能。 使用这些功能，你可以将 Purview 帐户连接到源来填充数据映射和数据目录，以便通过 Purview 开始浏览和管理数据。

## <a name="scanning"></a>扫描

在 Purview 帐户中[注册](manage-data-sources.md)数据源后，下一步是扫描数据源。 在扫描过程中会与数据源建立连接，并捕获技术元数据，例如名称、文件大小、列，等等。 它还会提取结构化数据源的架构，对架构应用分类，并[在你的 Purview 帐户连接到 Microsoft 365 安全与合规中心 (SCC) 的情况下应用敏感度标签](create-sensitivity-label.md)。 扫描过程可以在触发后立即运行，也可以按计划定期运行，使你的 Purview 帐户保持最新。

对于每次扫描，你可以应用自定义项，以便仅在源中扫描所需信息。

### <a name="scope-your-scan"></a>限定扫描范围

扫描某个源时，可以选择扫描整个数据源，或选择仅扫描特定实体（文件夹/表）。 可用选项取决于所扫描的源，可以针对一次性扫描和计划的扫描进行定义。

例如，[针对 Azure SQL 数据库创建和运行扫描](register-scan-azure-sql-database.md#creating-and-running-a-scan)时，可以选择要扫描的表，或选择整个数据库。

### <a name="scan-rule-set"></a>扫描规则集

扫描规则集决定了针对源之一运行扫描时扫描将查找的信息的类型。 可用规则取决于要扫描的源的类型，但包括应扫描的[文件类型](sources-and-scans.md#file-types-supported-for-scanning)以及需要的[分类](supported-classifications.md)类型等项。

许多数据源类型已存在可用的[系统扫描规则集](create-a-scan-rule-set.md#system-scan-rule-sets)，但是你也可以[创建自己的扫描规则集](create-a-scan-rule-set.md)来为组织定制扫描。

## <a name="ingestion"></a>引流

然后，扫描过程识别出的技术元数据或分类将被发送到“引入”。 引入过程负责填充数据映射，由 Purview 管理。  引入会分析来自扫描的输入，[应用资源集模式](concept-resource-sets.md#how-azure-purview-detects-resource-sets)，填充可用的[数据世系](concept-data-lineage.md)信息，然后自动加载数据映射。 只有在引入完成后，才能发现或策展资产/架构。 因此，如果扫描已完成，但你未在数据映射或目录中看到资产，则需要等待引入过程完成。

## <a name="next-steps"></a>后续步骤

有关对源进行扫描的详细信息或特定说明，请点击以下链接。

* 若要了解资源集，请参阅我们的[资源集文章](concept-resource-sets.md)。
* [如何注册并扫描 Azure SQL 数据库](register-scan-azure-sql-database.md#creating-and-running-a-scan)
* [Azure Purview 中的数据世系](catalog-lineage-user-guide.md)
