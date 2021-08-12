---
title: 从本地 MySQL 到 Azure Database for MySQL 的迁移指南测试计划
description: WWI 创建了一个包含一组 IT 和业务任务的测试计划。 要成功迁移，需执行所有测试。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: a41f995d7fcef217780da4c89c5bc3e0acdf6bbc
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110656728"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-test-plans"></a>从本地 MySQL 到 Azure Database for MySQL 的迁移指南测试计划

### <a name="overview"></a>概述

WWI 创建了一个包含一组 IT 和业务任务的测试计划。 要成功迁移，需执行所有测试。

测试：

  - 确保迁移的数据库与本地表保持一致性（相同的记录计数和查询结果）。

  - 确保性能是可接受的（应具有与在本地运行时相同的性能）。

  - 确保目标查询的性能满足规定的要求。

  - 确保本地和 Azure 网络之间具有可接受的网络连接。

  - 确保所有已识别的应用程序和用户都可以连接到已迁移的数据实例。

WWI 确定了一个迁移时间和时间范围，从太平洋时间晚上 10 点开始到凌晨 2 点结束。 如果在通过所有测试后，迁移未在目标时间凌晨 2 点（4 小时目标停机时间）之前完成，则会启动回滚过程。 记录了问题，以便将来迁移时参考。 所有迁移时间都根据可接受的业务时间表提前了并重新安排。

### <a name="sample-queries"></a>示例查询

对源和目标执行了一系列查询，以验证数据库迁移是否已成功。 以下查询和脚本将帮助确定迁移操作是否已将所有必需的数据库对象从源移动到目标。

#### <a name="table-rows"></a>表行

可以使用此查询来获取所有表和估计的行计数：

```
SELECT SUM(TABLE_ROWS)
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_SCHEMA = '{SchemaName}';
```

> [!NOTE]
> `INFORMATION_SCHEMA` 表将提供表中的一组估计值。 要更准确地了解表大小等指标，请使用 `innodb_stats_transient_sample_pages` 服务器参数增加页面样本大小。 增加此服务器值将强制分析更多页面并提供更准确的结果。

对每个表执行 `count(*)` SQL 语句以获得准确的行计数。 在大型表上运行此命令可能会花费大量时间。 以下脚本将生成一组 SQL 语句，可执行此语句以获取准确的计数：

```
SELECT CONCAT( 
    'SELECT "', 
    table_name, 
    '" AS table_name, COUNT(*) AS exact_row_count FROM `', 
    table_schema, 
    '`.`', 
    table_name, 
    '` UNION ' 
)  
FROM INFORMATION_SCHEMA.TABLES 
WHERE table_schema = '**my_schema**';
```

#### <a name="table-fragmentation"></a>表碎片

随着应用程序的持续使用，数据表可能会继续变大。 在某些情况下，数据可能不会增加，但会因为删除和更新而不断发生变化。 如果是这样，则数据库文件中可能存在大量碎片。 MySQL OPTIMIZE TABLE 语句可以减少物理存储空间需求，提高 I/O 效率。

可以通过运行以下命令[优化 MySQL 表](https://dev.mysql.com/doc/refman/8.0/en/optimize-table.html)：

`optimize table TABLE_NAME`

#### <a name="database-objects"></a>数据库对象

使用以下查询来确保已包含所有其他数据库对象。 尽管这些查询可以计算表行计数，但它们可能不表明特定数据库对象的版本。 例如，即使可能存在存储过程，但可能会在迁移开始和结束之间发生变化。 建议在迁移期间暂停开发数据库对象。

**用户**

```
SELECT DISTINCT 
        USER 
FROM 
        mysql.user 
WHERE 
        user <> '' order by user
```

**索引**

```
SELECT DISTINCT 
        INDEX_NAME 
FROM 
        information_schema.STATISTICS 
WHERE 
        TABLE_SCHEMA = '{SchemaName}'
```


**约束**

```
SELECT DISTINCT 
        CONSTRAINT_NAME 
FROM 
        information_schema.TABLE_CONSTRAINTS 
WHERE 
        CONSTRAINT_SCHEMA = '{SchemaName}'
```

**Views**

```
SELECT 
        TABLE_NAME 
FROM 
        information_schema.VIEWS 
WHERE 
        TABLE_SCHEMA = '{SchemaName}'
```

**存储过程**

```
SELECT 
        ROUTINE_NAME 
FROM 
        information_schema.ROUTINES 
WHERE 
        ROUTINE_TYPE = 'FUNCTION' and 
        ROUTINE_SCHEMA = '{SchemaName}'
```

**函数**

```
SELECT 
        ROUTINE_NAME 
FROM 
        information_schema.ROUTINES 
WHERE 
        ROUTINE_TYPE = 'PROCEDURE' and 
        ROUTINE_SCHEMA = '{SchemaName}'
```

**事件**

```
SELECT 
        EVENT_NAME 
FROM 
        INFORMATION_SCHEMA.EVENTS 
WHERE 
        EVENT_SCHEMA = '{SchemaName}'
```

### <a name="rollback-strategies"></a>回滚策略

上面的查询提供了回滚决策中使用的对象名称和计数的列表。 迁移用户可以通过检查源和目标对象计数来执行第一个对象验证步骤。 对象计数的差异不一定意味着需要回滚。 执行深入评估可以说明差异很小且很容易恢复。 对少数失败的对象执行手动迁移可能会解决此问题。 例如，如果已迁移所有表和数据行，只有少数函数被遗漏，则修复那些失败的对象并完成迁移。 如果数据库相对较小，可以清除 Azure Database for MySQL 实例并重启迁移。 大型数据库可能需要比提供的迁移时间更多的时间来确定缺失的对象。 需要停止并回滚迁移。

在迁移过程中，需要快速识别缺失的数据库对象。 每一分钟都很重要。 一种选择是将环境对象名称导出到文件，并使用数据比较工具快速识别缺失的对象。 另一种选择是导出源数据库对象名称，并将数据导入目标数据库环境临时表。 使用已编写脚本和经过测试的 SQL 语句比较数据 。 数据验证速度和准确度对迁移过程至关重要。 在迁移期间，不要依赖于手动读取和验证一长串数据库对象。 人为错误的可能性太大了。 最好是使用工具管理异常。

### <a name="wwi-scenario"></a>WWI 场景

WWI CIO 收到了确认报告，所有数据库对象都从本地数据库迁移到了 Azure Database for MySQL 实例。 数据库团队在迁移开始之前针对数据库运行了上述查询，并将所有结果保存到电子表格中。

源数据库架构信息用于验证目标迁移对象的保真度。

### <a name="checklist"></a>清单

  - 对测试查询编写脚本、进行测试以及做好执行准备。

  - 了解运行测试查询所需的时长，并将其作为记录的迁移时间表的一部分。

  - 为不同的可能结果准备好迁移和回滚策略。

  - 为迁移制定明确的事件时间表。  


> [!div class="nextstepaction"]
> [性能基线](./performance-baselines.md)