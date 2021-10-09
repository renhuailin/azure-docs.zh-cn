---
title: 通过示例了解 Azure 数据工厂定价
description: 本文使用详细的示例介绍并演示 Azure 数据工厂定价模型
author: shirleywangmsft
ms.author: shwang
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: pricing
ms.topic: conceptual
ms.date: 09/07/2021
ms.openlocfilehash: 38d6f8d8b96526c8ba190559a639985bedf96cff
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798553"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>通过示例了解数据工厂定价

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文使用详细的示例介绍并演示 Azure 数据工厂定价模型。

> [!NOTE]
> 以下这些示例中使用的价格是假设的，并不意味着实际定价。

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>将数据每隔一小时从 AWS S3 复制到 Azure Blob 存储

在此方案中，需按计划将数据每隔一小时从 AWS S3 复制到 Azure Blob 存储。

若要完成此方案，需使用以下项创建一个管道：

1. 使用输入数据集（适用于将要从 AWS S3 复制的数据）的复制活动。

2. Azure 存储上的数据的输出数据集。

3. 一个计划触发器，用于每隔一小时执行一次管道。

   :::image type="content" source="media/pricing-concepts/scenario1.png" alt-text="关系图显示含计划触发器的管道。在此管道中，复制活动可通过流向输入数据集来流向 AWS S3 链接服务，还可以通过流向输出数据集来流向 Azure 存储链接服务。":::

| **操作** | **类型和单元** |
| --- | --- |
| 创建链接的服务 | 2 个读/写实体  |
| 创建数据集 | 4 个读/写实体（2 个用于创建数据集，2 个用于链接的服务的引用） |
| 创建管道 | 3 个读/写实体（1 个用于创建管道，2 个用于数据集引用） |
| 获取管道 | 1 个读/写实体 |
| 运行管道 | 2 个活动运行（1 个用于触发器运行，1 个用于活动运行） |
| 复制数据假设：执行时间 = 10 分钟 | 10 \* 4 Azure Integration Runtime（默认 DIU 设置 = 4）有关数据集成单元和副本性能优化的详细信息，请参阅[此文](copy-activity-performance.md) |
| 监视管道假设：仅发生 1 次运行 | 检索到 2 个监视运行记录（1 个用于管道运行，1 个用于活动运行） |

**方案定价总计：$0.16811**

- 数据工厂操作 = **$0.0001**
  - 读取/写入 = 10\*00001 = $0.0001 [1 读/写 = $0.50/50000 = 0.00001]
  - 监视  = 2\*000005 = $0.00001 [1 监视 = $0.25/50000 = 0.000005]
- 管道业务流程 &amp; 执行 = **$0.168**
  - 活动运行 = 001\*2 = 0.002 [1 运行 = $1/1000 = 0.001]
  - 数据移动活动 = $0.166（以 10 分钟的执行时间按比例计算。 Azure Integration Runtime 上的定价为 $0.25/小时）

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>使用 Azure Databricks 按小时复制数据并进行转换

在此方案中，需使用 Azure Databricks 按计划将数据每隔一小时从 AWS S3 复制到 Azure Blob 存储并对数据进行转换。

若要完成此方案，需使用以下项创建一个管道：

1. 一个使用输入数据集（适用于将要从 AWS S3 复制的数据）和输出数据集（适用于 Azure 存储上的数据）的复制活动。
2. 一个用于数据转换的 Azure Databricks 活动。
3. 一个计划触发器，用于每隔一小时执行一次管道。

:::image type="content" source="media/pricing-concepts/scenario2.png" alt-text="关系图显示具有计划触发器的管道。在管道中，将活动流复制到输入数据集、输出数据集和 DataBricks 活动，该活动在 Azure Databricks 上运行。输入数据集流向 A W S S3 链接服务。输出数据集流向 Azure 存储链接的服务。":::

| **操作** | **类型和单元** |
| --- | --- |
| 创建链接的服务 | 3 个读/写实体  |
| 创建数据集 | 4 个读/写实体（2 个用于创建数据集，2 个用于链接的服务的引用） |
| 创建管道 | 3 个读/写实体（1 个用于创建管道，2 个用于数据集引用） |
| 获取管道 | 1 个读/写实体 |
| 运行管道 | 3 个活动运行（1 个用于触发器运行，2 个用于活动运行） |
| 复制数据假设：执行时间 = 10 分钟 | 10 \* 4 Azure Integration Runtime（默认 DIU 设置 = 4）有关数据集成单元和副本性能优化的详细信息，请参阅[此文](copy-activity-performance.md) |
| 监视管道假设：仅发生 1 次运行 | 检索到 3 个监视运行记录（1 个用于管道运行，2 个用于活动运行） |
| 执行 Databricks 活动假设：执行时间 = 10 分钟 | 10 分钟执行外部管道活动 |

**方案定价总计：$0.16916**

- 数据工厂操作 = **$0.00012**
  - 读取/写入 = 11\*00001 = $0.00011 [1 读/写 = $0.50/50000 = 0.00001]
  - 监视  = 3\*000005 = $0.00001 [1 监视 = $0.25/50000 = 0.000005]
- 管道业务流程 &amp; 执行 = **$0.16904**
  - 活动运行 = 001\*3 = 0.003 [1 运行 = $1/1000 = 0.001]
  - 数据移动活动 = $0.166（以 10 分钟的执行时间按比例计算。 Azure Integration Runtime 上的定价为 $0.25/小时）
  - 外部管道活动 = $0.000041（以 10 分钟的执行时间按比例计算。 Azure Integration Runtime 上的定价为 $0.00025/小时）

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>使用动态参数按小时复制数据并进行转换

在此方案中，需使用 Azure Databricks（使用脚本中的动态参数）按计划将数据每隔一小时从 AWS S3 复制到 Azure Blob 存储并进行转换。

若要完成此方案，需使用以下项创建一个管道：

1. 一个使用输入数据集（适用于将要从 AWS S3 复制的数据）和输出数据集（适用于 Azure 存储上的数据）的复制活动。
2. 一个查找活动，用于将参数动态传递到转换脚本。
3. 一个用于数据转换的 Azure Databricks 活动。
4. 一个计划触发器，用于每隔一小时执行一次管道。

:::image type="content" source="media/pricing-concepts/scenario3.png" alt-text="关系图显示具有计划触发器的管道。在管道中，将活动流复制到输入数据集、输出数据集和流向 DataBricks 活动的查找活动，该活动在 Azure Databricks 上运行。输入数据集流向 A W S S3 链接服务。输出数据集流向 Azure 存储链接的服务。":::

| **操作** | **类型和单元** |
| --- | --- |
| 创建链接的服务 | 3 个读/写实体  |
| 创建数据集 | 4 个读/写实体（2 个用于创建数据集，2 个用于链接的服务的引用） |
| 创建管道 | 3 个读/写实体（1 个用于创建管道，2 个用于数据集引用） |
| 获取管道 | 1 个读/写实体 |
| 运行管道 | 4 个活动运行（1 个用于触发器运行，3 个用于活动运行） |
| 复制数据假设：执行时间 = 10 分钟 | 10 \* 4 Azure Integration Runtime（默认 DIU 设置 = 4）有关数据集成单元和副本性能优化的详细信息，请参阅[此文](copy-activity-performance.md) |
| 监视管道假设：仅发生 1 次运行 | 检索到 4 个监视运行记录（1 个用于管道运行，3 个用于活动运行） |
| 执行查找活动假设：执行时间 = 1 分钟 | 1 分钟执行管道活动 |
| 执行 Databricks 活动假设：执行时间 = 10 分钟 | 10 分钟执行外部管道活动 |

**方案定价总计：$0.17020**

- 数据工厂操作 = **$0.00013**
  - 读取/写入 = 11\*00001 = $0.00011 [1 读/写 = $0.50/50000 = 0.00001]
  - 监视  = 4\*000005 = $0.00002 [1 监视 = $0.25/50000 = 0.000005]
- 管道业务流程 &amp; 执行 = **$0.17007**
  - 活动运行 = 001\*4 = 0.004 [1 运行 = $1/1000 = 0.001]
  - 数据移动活动 = $0.166（以 10 分钟的执行时间按比例计算。 Azure Integration Runtime 上的定价为 $0.25/小时）
  - 管道活动 = $0.00003（以 1 分钟的执行时间按比例计算。 Azure Integration Runtime 上的定价为 $0.002/小时）
  - 外部管道活动 = $0.000041（以 10 分钟的执行时间按比例计算。 Azure Integration Runtime 上的定价为 $0.00025/小时）

## <a name="run-ssis-packages-on-azure-ssis-integration-runtime"></a>在 Azure-SSIS 集成运行时上运行 SSIS 包

Azure-SSIS 集成运行时 (IR) 是 Azure 虚拟机 (VM) 的一个专用群集，用于在 Azure 数据工厂 (ADF) 中执行 SSIS 包。 当你进行预配后，它将是你专用的，因此，只要你保持它的运行状态，无论你是否用它来执行 SSIS 包，它都会像任何其他专用 Azure VM 一样被收取费用。 有关其运行成本，可以在 ADF 门户的“设置”窗格中看到每小时估算值，例如：  

:::image type="content" source="media/pricing-concepts/ssis-pricing-example.png" alt-text="SSIS 定价示例":::

在以上示例中，如果使 Azure-SSIS IR 保持运行状态 2 小时，则费用为：2（小时）x 1.158 美元/小时 = 2.316 美元。

若要管理 Azure-SSIS IR 运行成本，可以纵向缩减 VM 大小、横向缩减群集大小、通过 Azure 混合权益 (AHB) 选项自带 SQL Server 许可证，这样可以节省大量成本，请参阅 [Azure-SSIS IR 定价](https://azure.microsoft.com/pricing/details/data-factory/ssis/)，并且/或者在方便的时候/根据需求/刚好是处理 SSIS 工作负载时，启动和停止 Azure-SSIS IR，请参阅[重新配置 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md#to-reconfigure-an-azure-ssis-ir) 和[计划 Azure-SSIS IR](how-to-schedule-azure-ssis-integration-runtime.md)。

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>使用适合正常工作日的映射数据流调试

作为数据工程师，Sam 每天负责设计、构建和测试映射数据流。 Sam 早上登录到 ADF UI，为数据流启用“调试”模式。 调试会话的默认 TTL 为 60 分钟。 Sam 一整天 8 小时都在工作，因此调试会话永不过期。 因此，Sam 每天的费用将是：

8（小时）x 8（进行了计算优化的核心数）x $0.193 = $12.35

同时，另一位数据工程师 Chris 也会登录到 ADF 浏览器 UI 来完成数据分析和 ETL 设计工作。 Chris 不像 Sam 一样全天都在 ADF 中工作。 Chris 只需要使用 1 小时数据流调试程序（与上面的 Sam 同天同时段）。 以下是 Chris 因使用调试而产生的费用：

1（小时）x 8（常规用途核心数）x $0.274 = $2.19

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>使用映射数据流转换 blob 存储中的数据

在此方案中，你想要按照每小时一次的节奏在 ADF 映射数据流中直观地转换 Blob 存储中的数据。

若要完成此方案，需使用以下项创建一个管道：

1. 包含转换逻辑的数据流活动。

2. Azure 存储中的数据的输入数据集。

3. Azure 存储上的数据的输出数据集。

4. 一个计划触发器，用于每隔一小时执行一次管道。

| **操作** | **类型和单元** |
| --- | --- |
| 创建链接的服务 | 2 个读/写实体  |
| 创建数据集 | 4 个读/写实体（2 个用于创建数据集，2 个用于链接的服务的引用） |
| 创建管道 | 3 个读/写实体（1 个用于创建管道，2 个用于数据集引用） |
| 获取管道 | 1 个读/写实体 |
| 运行管道 | 2 个活动运行（1 个用于触发器运行，1 个用于活动运行） |
| 数据流假设：执行时间 = 10 分钟 + 10 分钟 TTL | 10 \* 16 个 TTL 为 10 的常规计算核心 |
| 监视管道假设：仅发生 1 次运行 | 检索到 2 个监视运行记录（1 个用于管道运行，1 个用于活动运行） |

方案定价总计：$1.4631

- 数据工厂操作 = **$0.0001**
  - 读取/写入 = 10\*00001 = $0.0001 [1 读/写 = $0.50/50000 = 0.00001]
  - 监视  = 2\*000005 = $0.00001 [1 监视 = $0.25/50000 = 0.000005]
- 管道业务流程 &amp; 执行 = **$1.463**
  - 活动运行 = 001\*2 = 0.002 [1 运行 = $1/1000 = 0.001]
  - 数据流活动 = $1.461，以 20 分钟（10 分钟执行时间 + 10 分钟 TTL）的时间按比例计算。 在具有 16 个常规计算核心的 Azure Integration Runtime 上定价为 $0.274/小时

## <a name="data-integration-in-azure-data-factory-managed-vnet"></a>Azure 数据工厂托管 VNET 中的数据集成
在此方案中，你想要删除 Azure Blob 存储中的原始文件并将数据从 Azure SQL 数据库复制到 Azure Blob 存储。 你将在不同的管道上两次执行此操作。 这两个管道的执行时间重叠。
:::image type="content" source="media/pricing-concepts/scenario-4.png" alt-text="方案 4":::
若要完成此方案，你需要创建两个包含以下项的管道：
  - 一个管道活动 - 删除活动。
  - 一个复制活动，它使用要从 Azure Blob 存储复制的数据的输入数据集。
  - Azure SQL 数据库中的数据的输出数据集。
  - 一个计划触发器，用于执行管道。


| **操作** | **类型和单元** |
| --- | --- |
| 创建链接的服务 | 4 个读/写实体 |
| 创建数据集 | 8 个读/写实体（4 个用于创建数据集，4 个用于链接服务的引用） |
| 创建管道 | 6 个读/写实体（2 个用于创建管道，4 个用于数据集引用） |
| 获取管道 | 2 个读/写实体 |
| 运行管道 | 6 个活动运行（2 个用于触发器运行，4 个用于活动运行） |
| 执行删除活动：每次执行时间 = 5 分钟。第一个管道中的删除活动执行时间为 10:00 AM UTC 到 10:05 AM UTC。 第二个管道中的删除活动执行时间为 10:02 AM UTC 到 10:07 AM UTC。|托管 VNET 中的管道活动执行时间总共为 7 分钟。 在托管 VNET 中，管道活动最多支持 50 个并发活动。 管道活动有 60 分钟的生存时间 (TTL)|
| 复制数据假设：每次执行时间 = 10 分钟。第一个管道中的复制活动执行时间为 10:06 AM UTC 到 10:15 AM UTC。 第二个管道中的复制活动执行时间为 10:08 AM UTC 到 10:17 AM UTC。 | 10 * 4 Azure Integration Runtime（默认 DIU 设置 = 4）有关数据集成单元和复制性能优化的详细信息，请参阅[此文](copy-activity-performance.md) |
| 监视管道假设：仅进行了 2 次运行 | 检索到 6 个监视运行记录（2 个用于管道运行，4 个用于活动运行） |


**方案定价总计：$1.45523**

- 数据工厂操作 = $0.00023
  - 读/写 = 20*00001 = $0.0002 [1 读/写 = $0.50/50000 = 0.00001]
  - 监视 = 6*000005 = $0.00003 [1 监视 = $0.25/50000 = 0.000005]
- 管道业务流程与执行 = $1.455
  - 活动运行 = 0.001*6 = 0.006 [1 运行 = $1/1000 = 0.001]
  - 数据移动活动 = $0.333（以 10 分钟的执行时间按比例计算。 Azure Integration Runtime 上的定价为 $0.25/小时）
  - 管道活动 = $1.116（以 7 分钟的执行时间加上 60 分钟的 TTL，按比例计算。 Azure Integration Runtime 上的定价为 $1/小时）

> [!NOTE] 
> 这些价格仅用作示例。

**常见问题解答**

问：如果我想要运行 50 个以上的管道活动，是否可以同时执行这些活动？

答：最多将允许 50 个并发的管道活动。  第 51 个管道活动将被排入队列，直到有“空位”开放。 外部活动也是如此。 最多将允许 800 个并发的外部活动。

## <a name="next-steps"></a>后续步骤

了解 Azure 数据工厂的定价以后，即可开始操作！

- [使用 Azure 数据工厂 UI 创建数据工厂](quickstart-create-data-factory-portal.md)

- [Azure 数据工厂简介](introduction.md)

- [Azure 数据工厂中的视觉对象创作](author-visually.md)
