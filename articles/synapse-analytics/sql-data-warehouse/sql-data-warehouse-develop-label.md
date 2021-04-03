---
title: 使用标签来检测查询
description: 有关在 Azure Synapse Analytics 中使用标签检测专用 SQL 池的查询的技巧。
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7958caf71658dcdcbf31bac84697931e9049452f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462744"
---
# <a name="using-labels-to-instrument-queries-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用标签检测专用 SQL 池的查询

本文介绍了使用标签在专用 SQL 池中检测查询以开发解决方案的技巧。

## <a name="what-are-labels"></a>什么是标签？

专用 SQL 池支持称为“查询标签”的概念。 在继续之前，让我们看一个示例：

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

最后一行将字符串“My Query Label”标记为查询。 此标记很有用，因为可以通过 DMV 查询标签。

对标签进行查询提供了一种用于定位有问题的查询并帮助查明 ELT 运行进度的机制。

良好的命名约定确实很有帮助。 例如，让标签以 PROJECT、PROCEDURE、STATEMENT 或 COMMENT 开头可在源代码管理的所有代码中唯一地标识查询。

以下查询使用动态管理视图按标签进行搜索：

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> 查询时，必须以方括号或双引号括住文字标签。 Label 是一个保留字，不将其分隔会导致错误。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅 [开发概述](sql-data-warehouse-overview-develop.md)。
