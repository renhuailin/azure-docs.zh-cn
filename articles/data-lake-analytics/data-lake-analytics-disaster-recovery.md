---
title: Azure Data Lake Analytics 灾难恢复指南
description: 了解如何规划 Azure Data Lake Analytics 帐户的灾难恢复。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: ce5e96bf464eebb9b91e3ffac9312e89df9cbabe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220969"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics 灾难恢复指南

Azure Data Lake Analytics 是一项按需分析作业服务，用于简化大数据。 无需部署、配置和调整硬件，只需编写查询即可转换数据并提取有价值的见解。 通过将表盘设置为所需值，该分析服务就可以立即处理任何规模的作业。 只需为运行作业付费，让服务变得更为经济高效。 本文指导如何保护作业，防范极其少见的全区域服务中断或意外删除行为。

## <a name="disaster-recovery-guidance"></a>灾难恢复指南

使用 Azure Data Lake Analytics 时，必须准备好自己的灾难恢复计划。 本文介绍如何构建灾难恢复计划。 下面这些额外资源可以帮助创建自己的计划：
- [Azure 应用程序的故障和灾难恢复](/azure/architecture/reliability/disaster-recovery)
- [Azure 复原技术指南](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>最佳实践和方案指南

可以在区域中的 ADLA 帐户中运行重复的 U-SQL 作业，该区域可读取和写入 U-SQL 表以及非结构化数据。  通过执行以下步骤来准备灾难：

1. 在将在中断期间使用的次要区域中创建 ADLA 和 ADLS 帐户。

   > [!NOTE]
   > 由于帐户名称是全局唯一的，因此请使用一致命名方案来指示哪个帐户是辅助帐户。

2. 对于非结构化数据，请参阅 [Azure Data Lake Storage Gen1 数据灾难恢复指南](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. 对于存储在 ADLA 表和数据库中的结构化数据，请创建元数据项目的副本，如数据库、表、表值函数和程序集。 生产中发生变化时，需要定期重新同步这些工件。 例如，必须通过以下方式将新插入的数据复制到次要区域：先复制数据，然后将其插入到辅助表中。

   > [!NOTE]
   > 这些对象名称的作用域为辅助帐户，且不是全局唯一的，因此它们的名称可以与主生产帐户中的名称相同。

在中断期间，需要更新脚本，使输入路径指向辅助终结点。 然后，用户将其作业提交到次要区域中的 ADLA 帐户。 该作业的输出随后将写入到次要区域中的 ADLA 和 ADLS 帐户。

## <a name="next-steps"></a>后续步骤

[Azure Data Lake Storage Gen1 数据灾难恢复指南](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
