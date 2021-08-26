---
title: 使用 Azure 数据工厂中的映射数据流处理错误行
description: 了解如何使用映射数据流处理 Azure 数据工厂中的 SQL 截断错误。
author: kromerm
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.date: 11/22/2020
ms.author: makromer
ms.openlocfilehash: f4f4c1c334bb3720118b050947da6345116ef22f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638573"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>使用映射数据流处理数据工厂中的 SQL 截断错误行

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用映射数据流时数据工厂中的一个常见场景是，将转换后的数据写入 Azure SQL 数据库中的数据库。 在此场景中，必须防止的一种常见错误情况是可能的列截断。

在 ADF 数据流中将数据写入数据库接收器时，有两种主要方法可用于正常处理错误：

* 在处理数据库数据时，将接收器[错误行处理](./connector-azure-sql-database.md#error-row-handling)设置为“出错时继续”。 这是一种自动的综合方法，不需要在数据流中使用自定义逻辑。
* 或者，按照以下步骤提供不适合目标字符串列的列日志记录，从而允许数据流继续运行。

> [!NOTE]
> 启用自动错误行处理时，与下面编写你自己的错误处理逻辑这一方法不同，ADF 执行 2 阶段操作以捕获错误时将引起较小的性能损失，并且将采取额外的步骤。

## <a name="scenario"></a>方案

1. 我们有一个包含名为“name”的 ```nvarchar(5)``` 列的目标数据库表。

2. 在数据流中，我们想要将接收器中的电影标题映射到该目标“name”列。

    ![电影数据流 1](media/data-flow/error4.png)
    
3. 问题在于，电影标题无法全部放在接收器列中，这一列只能容纳 5 个字符。 在执行此数据流时，你将收到如下错误：```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

此视频演示了在数据流中设置错误行处理逻辑的示例：
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>如何围绕此情况进行设计

1. 在此场景中，“name”列的最大长度为 5 个字符。 因此，让我们添加一个有条件拆分转换，该转换允许我们记录“title”长度超过 5 个字符的行，同时还允许可以放入该空间的其余行写入数据库。

    ![有条件拆分 (conditional split)](media/data-flow/error1.png)

2. 此有条件拆分转换将“title”的最大长度定义为 5 个字符。 任何小于或等于 5 的行都将进入 ```GoodRows``` 流中。 任何大于 5 的行都将进入 ```BadRows``` 流中。

3. 现在，我们需要记录失败的行。 将接收器转换添加到 ```BadRows``` 流以进行日志记录。 在这里，我们将自动映射所有字段，以便记录完整的事务记录。 这是一个以文本分隔的 CSV 文件输出，流向 Blob 存储中的单个文件。 我们将调用日志文件“badrows.csv”。

    ![错误行](media/data-flow/error3.png)
    
4. 已完成的数据流如下所示。 现在，我们可以拆分错误行以避免 SQL 截断错误，并将这些条目放入日志文件中。 同时，成功的行可以继续写入目标数据库。

    ![完成数据流](media/data-flow/error2.png)

5. 如果在接收器转换中选择“错误行处理”选项并设置“输出错误行”，ADF 将自动生成行数据的 CSV 文件输出以及驱动程序报告的错误消息。 无需手动将该逻辑添加到具有该替代选项的数据流。 使用此选项时，ADF 可以实现一种两阶段方法来捕获错误并记录这些错误，但会出现较小的性能损失。

    ![完成包含错误行的数据流](media/data-flow/error-row-3.png)

## <a name="next-steps"></a>后续步骤

* 使用映射数据流[转换](concepts-data-flow-overview.md)来生成数据流逻辑的其余部分。