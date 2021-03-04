---
title: 连接到 Azure 数据工厂
description: 本文介绍如何连接 Azure 数据工厂和 Azure 监控范围来跟踪数据沿袭。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/03/2021
ms.openlocfilehash: 6a71999f0896a5d056b7d0b38be4d494c347e9f9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049366"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>如何连接 Azure 数据工厂和 Azure 监控范围

本文档介绍了使用 Azure 监控范围帐户连接 Azure 数据工厂帐户以跟踪数据沿袭所需的步骤。 该文档还介绍了覆盖率范围和支持的沿袭模式的详细信息。

## <a name="view-existing-data-factory-connections"></a>查看现有的数据工厂连接

多个 Azure 数据工厂可以连接到单个 Azure 监控范围数据目录来推送沿袭信息。 当前限制允许从监控范围管理中心一次连接10个数据工厂帐户。 若要显示连接到监控范围数据目录的数据工厂帐户的列表，请执行以下操作：

1. 选择左侧导航窗格中的 " **管理中心** "。
2. 在 " **外部连接**" 下，选择 " **数据工厂连接**"。
3. 此时将显示 "数据工厂连接" 列表。

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="显示数据工厂连接列表的屏幕截图。" lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. 请注意连接 **状态** 的各个值：

    - **已连接**：数据工厂已连接到数据目录。
    - **断开连接**：数据工厂有权访问该目录，但它已连接到另一个目录。 因此，不会自动向目录报告数据沿袭。
    - **CannotAccess**：当前用户无法访问数据工厂，因此连接状态为 "未知"。
 >[!Note]
 >若要查看数据工厂连接，需要为其分配监控范围角色之一：
 >- 参与者
 >- 所有者
 >- 读取器
 >- 用户访问管理员

## <a name="create-new-data-factory-connection"></a>创建新的数据工厂连接

>[!Note]
>若要添加或删除数据工厂连接，需要为其分配监控范围角色之一：
>- “所有者”
>- 用户访问管理员
>
> 除此之外，它还要求用户成为数据工厂的 "所有者" 或 "参与者"。 

按照以下步骤将现有的数据工厂帐户连接到监控范围数据目录。

1. 选择左侧导航窗格中的 " **管理中心** "。
2. 在 " **外部连接**" 下，选择 " **数据工厂连接**"。
3. 在 " **数据工厂连接** " 页上，选择 " **新建**"。

4. 从列表中选择数据工厂帐户，然后选择 **"确定"**。 还可以按订阅名称进行筛选以限制列表。

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="显示如何连接 Azure 数据工厂的屏幕截图。" lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    如果数据工厂已连接到当前监控范围帐户，或者数据工厂没有托管标识，则可能会禁用某些数据工厂实例。

    如果任何所选数据工厂已连接到其他监控范围帐户，则将显示一条警告消息。 通过选择 "确定"，会断开与其他监控范围帐户的数据工厂连接。 不需要其他确认。


    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="显示中断 Azure 数据工厂的警告的屏幕截图。" lightbox="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png":::

>[!Note]
>现在支持一次添加不超过10个数据工厂。 如果要一次添加10个以上的数据工厂，请提交支持票证。

### <a name="how-does-the-authentication-work"></a>身份验证的工作原理是什么？

当监控范围用户注册他们有权访问的数据工厂时，后端会发生以下情况：

1. **数据工厂托管标识** 将添加到监控范围 RBAC Role：**监控范围 Data 陈列**。

    :::image type="content" source="./media/how-to-link-azure-data-factory/adf-msi.png" alt-text="显示 Azure 数据工厂 MSI 的屏幕截图。" lightbox="./media/how-to-link-azure-data-factory/adf-msi.png":::
     
2. 需要重新执行数据工厂管道，以便沿袭元数据可以推送回监控范围。
3. 执行后，数据工厂元数据将推送到监控范围。

### <a name="remove-data-factory-connections"></a>删除数据工厂连接
若要删除数据工厂连接，请执行以下操作：

1. 在 " **数据工厂连接** " 页上，选择一个或多个数据工厂连接旁边的 " **删除** " 按钮。
2. 在弹出窗口中选择 " **确认** "，删除所选的数据工厂连接。

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="显示如何选择数据工厂以删除连接的屏幕截图。" lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="configure-a-self-hosted-integration-runtime-to-collect-lineage"></a>配置自承载 Integration Runtime 以收集沿袭

数据工厂复制活动的沿袭可用于本地数据存储（如 SQL 数据库）。 如果运行自承载集成运行时与 Azure 数据工厂的数据移动，并且想要捕获 Azure 监控范围中的沿袭，请确保版本为5.0 或更高版本。 有关自承载集成运行时的详细信息，请参阅 [创建和配置自承载集成运行时](../data-factory/create-self-hosted-integration-runtime.md)。

## <a name="supported-azure-data-factory-activities"></a>支持的 Azure 数据工厂活动

Azure 监控范围从以下 Azure 数据工厂活动中捕获运行时沿袭：

- [复制数据](../data-factory/copy-activity-overview.md)
- [数据流](../data-factory/concepts-data-flow-overview.md)
- [执行 SSIS 包](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)

> [!IMPORTANT]
> 如果源或目标使用不受支持的数据存储系统，Azure 监控范围会丢弃沿袭。

数据工厂和监控范围之间的集成仅支持数据工厂支持的数据系统子集，如以下各节中所述。

### <a name="data-factory-copy-activity-support"></a>数据工厂复制活动支持

| 数据存储 | 支持 | 
| ------------------- | ------------------- | 
| Azure Blob 存储 | 是 |
| Azure 认知搜索 | 是 | 
| Azure Cosmos DB (SQL API) \* | 是 | 
| Azure Cosmos DB 的适用于 MongoDB 的 API \* | 是 |
| Azure 数据资源管理器 \* | 是 | 
| Azure Data Lake Storage Gen1 | 是 | 
| Azure Data Lake Storage Gen2 | 是 | 
| 用于 Maria DB 的 Azure 数据库 \* | 是 | 
| Azure Database for MySQL \* | 是 | 
| Azure Database for PostgreSQL \* | 是 |
| Azure 文件存储 | 是 | 
| Azure SQL 数据库 \* | 是 | 
| Azure SQL 托管实例 \* | 是 | 
| Azure Synapse 分析 \* | 是 | 
| Azure 表存储 \* | 是 |
| SQL Server \* | 是 | 
| Amazon S3 | 是 | 
| 义项 \* | 是 | 
| SAP ECC \* | 是 |
| SAP 表 \* | 是 |
| Teradata \* | 是 |

*\* Azure 监控范围当前不支持沿袭或扫描的查询或存储过程。沿袭仅限于表和视图源。*

> [!Note]
> 沿袭功能在数据工厂复制活动中具有一定的性能开销。 对于在监控范围中设置数据工厂连接的用户，你可能会发现某些复制作业要花费更长时间才能完成。 大多数情况下，影响不会忽略。 如果复制作业的完成时间比平时长得多，请联系支持人员进行时间比较。

#### <a name="known-limitations-on-copy-activity-lineage"></a>复制活动沿袭的已知限制

目前，如果使用以下复制活动功能，则不支持沿袭：

- 使用二进制格式将数据复制到 Azure Data Lake Storage Gen1。
- 使用 PolyBase 或 COPY 语句将数据复制到 Azure Synapse Analytics 中。
- 二进制、分隔文本、Excel、JSON 和 XML 文件的压缩设置。
- 适用于 Azure SQL 数据库、Azure SQL 托管实例、Azure Synapse Analytics、SQL Server 和 SAP 表的源分区选项。
- 将数据复制到基于文件的接收器，并为每个文件设置最大行数。
- 在复制过程中添加其他列。

### <a name="data-factory-data-flow-support"></a>数据工厂数据流支持

| 数据存储 | 支持 |
| ------------------- | ------------------- | 
| Azure Blob 存储 | 是 |
| Azure Data Lake Storage Gen1 | 是 |
| Azure Data Lake Storage Gen2 | 是 |
| Azure SQL 数据库 \* | 是 |
| Azure Synapse 分析 \* | 是 |

*\* Azure 监控范围当前不支持沿袭或扫描的查询或存储过程。沿袭仅限于表和视图源。*

### <a name="data-factory-execute-ssis-package-support"></a>数据工厂执行 SSIS 包支持

| 数据存储 | 支持 |
| ------------------- | ------------------- |
| Azure Blob 存储 | 是 |
| Azure Data Lake Storage Gen1 | 是 |
| Azure Data Lake Storage Gen2 | 是 |
| Azure 文件存储 | 是 |
| Azure SQL 数据库 \* | 是 |
| Azure SQL 托管实例 \*| 是 |
| Azure Synapse 分析 \* | 是 |
| SQL Server \* | 是 |

*\* Azure 监控范围当前不支持沿袭或扫描的查询或存储过程。沿袭仅限于表和视图源。*

> [!Note]
> Azure Data Lake Storage Gen2 现已正式发布。 我们建议你立即开始使用它。 有关详细信息，请参阅[产品页](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/)。

## <a name="supported-lineage-patterns"></a>支持的沿袭模式

Azure 监控范围支持多种沿袭模式。 生成的沿袭数据基于数据工厂活动中使用的源和接收器的类型。 尽管数据工厂支持80多个源和接收器，但 Azure 监控范围仅支持一个子集，如 [受支持的 Azure 数据工厂活动](#supported-azure-data-factory-activities)中所列。

若要将数据工厂配置为发送沿袭信息，请参阅 [沿袭入门](catalog-lineage-user-guide.md#get-started-with-lineage)。

在沿袭视图中查找信息的其他方法包括：

- 在 " **沿袭** " 选项卡中，将鼠标悬停在形状上，以在工具提示中预览有关资产的其他信息。
- 选择节点或边缘以查看其所属的资产类型或切换资产。
- 数据集的列将显示在 " **沿袭** " 选项卡的左侧。有关列级沿袭的详细信息，请参阅 [数据集列沿袭](catalog-lineage-user-guide.md#dataset-column-lineage)。

### <a name="data-lineage-for-11-operations"></a>1:1 操作的数据沿袭

捕获数据沿袭的最常见的模式是将数据从单个输入数据集移到单个输出数据集，并在两者之间进行处理。

下面是此模式的示例：

- 1源/输入： *Customer* (SQL 表) 
- 1接收器/输出： *Customer1.csv* (Azure Blob) 
- 1进程： *CopyCustomerInfo1 \#Customer1.csv* (数据工厂复制活动) 

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="显示一到一个数据工厂复制操作的沿袭的屏幕截图。" lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage.png":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>支持1:1 沿袭和通配符的数据移动

捕获沿袭的另一种常见情况是使用通配符将文件从单个输入数据集复制到单个输出数据集。 通配符允许复制活动匹配多个文件，以便使用文件名称的常见部分进行复制。 Azure 监控范围捕获相应复制活动复制的每个单独文件的文件级沿袭。

下面是此模式的示例：

* 源/输入： *CustomerCall \** (ADLS Gen2 路径) 
* 接收器/输出： *CustomerCall \** (Azure blob 文件) 
* 1进程： *CopyGen2ToBlob \#CustomerCall.csv* (数据工厂复制活动)   

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="显示支持通配符的一对复制操作的沿袭的屏幕截图。" lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>采用 n：1沿袭的数据移动

您可以使用数据流活动执行合并、联接等数据操作。 可以使用多个源数据集生成目标数据集。 在此示例中，Azure 监控范围会将各个输入文件的文件级沿袭捕获到作为数据流活动一部分的 SQL 表。

下面是此模式的示例：

* 2源/输入： *Customer.csv*、 *Parquet* (ADLS Gen2 路径) 
* 1接收器/输出： *公司数据* (Azure SQL 表) 
* 1进程： *DataFlowBlobsToSQL* (数据工厂数据流活动) 

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="显示 n 到 A D F 数据流操作的沿袭的屏幕截图。" lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>资源集的沿袭

资源集是目录中的一个逻辑对象，表示基础存储中的多个分区文件。 有关详细信息，请参阅 [了解资源集](concept-resource-sets.md)。 当 Azure 监控范围捕获 Azure 数据工厂中的沿袭时，它将应用这些规则以规范化单个分区文件并创建一个逻辑对象。

在以下示例中，将从 Azure Blob 生成 Azure Data Lake Gen2 资源集：

* 1源/输入： (Azure Blob 的 *员工 \_management.csv*) 
* 1接收器/输出： *Employee \_management.csv* (Azure Data Lake 第2代) 
* 1进程： *CopyBlobToAdlsGen2 \_ RS* (数据工厂复制活动) 

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="显示资源集的沿袭的屏幕截图。" lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>后续步骤

- [目录沿袭用户指南](catalog-lineage-user-guide.md)
- [链接到 Azure 数据共享进行沿袭](how-to-link-azure-data-share.md)
