---
title: 用于 Azure Data Lake 的 U SQL UDO 可编程性指南
description: 了解有关 SCRIPT.USQL 的详细信息，请参阅 U-SQL UDO 可编程性 Azure Data Lake Analytics。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 02360c68e5e830ceee69075fd5532b126d85bec2
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512520"
---
# <a name="u-sql-user-defined-objects-overview"></a>U-SQL 用户定义对象概述


## <a name="u-sql-user-defined-objects-udo"></a>U SQL：用户定义的对象： UDO
U-SQL 可让你定义自定义可编程性对象，此类对象称为用户定义的对象 (UDO)。

下面是 U-SQL 中的 UDO 列表：

* 用户定义的提取器
    * 逐行提取
    * 用于实现数据提取窗体自定义结构化的文件

* 用户定义的输出器
    * 逐行输出
    * 用于输出自定义数据类型或用于自定义文件格式

* 用户定义的处理器
    * 提取一行并生成一行
    * 用于减少列数或生成新列，这些列具有从存在的列集派生的值

* 用户定义的应用器
    * 提取一行并生成 0 到 n 行
    * 与 OUTER/CROSS APPLY 配合使用

* 用户定义的合并器
    * 合并行集 -- 用户定义的联接

* 用户定义的化简器
    * 提取 n 行并生成一行
    * 用于减少行数

通常在 U-SQL 脚本中将 UDO 作为以下 U-SQL 语句的一部分进行显式调用：

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

> [!NOTE]  
> UDO 被限制使用至多 0.5 Gb 内存。  此内存限制对本地执行不适用。

## <a name="next-steps"></a>后续步骤
* [U-SQL 可编程性指南-概述](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 可编程性指南-UDT 和 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)