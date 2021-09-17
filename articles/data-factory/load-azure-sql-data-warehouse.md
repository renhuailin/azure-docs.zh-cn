---
title: 将数据加载到 Azure Synapse Analytics 中
titleSuffix: Azure Data Factory & Azure Synapse
description: 使用 Azure 数据工厂或 Synapse 管道将数据复制到 Azure Synapse Analytics 中
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 08/24/2021
ms.openlocfilehash: b297e47e3b1cb890312b6f7a566eb4f033e6612a
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824117"
---
# <a name="load-data-into-azure-synapse-analytics-using-azure-data-factory-or-a-synapse-pipeline"></a>使用 Azure 数据工厂或 Synapse 管道将数据加载到 Azure Synapse Analytics 中

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 是一种基于云的向外扩展数据库，可以处理大量数据（关系数据和非关系数据）。 Azure Synapse Analytics 在大规模并行处理 (MPP) 体系结构的基础上构建，已针对企业数据仓库工作负荷进行优化。 它通过灵活地缩放存储以及独立计算提供云灵活性。

如今，Azure Synapse Analytics 入门变得前所未有的简单。 Azure 数据工厂及其在 Azure Synapse 中的等效管道功能提供了完全托管且基于云的数据集成服务。 该服务可用于使用现有系统中的数据填充 Azure Synapse Analytics，在生成分析解决方案时节省时间。

以下是使用 Azure 数据工厂和 Synapse 管道将数据加载到 Azure Synapse Analytics 的优点：

* **轻松设置**：无需脚本的直观 5 步向导。
* **丰富的数据存储支持**：对一组丰富的本地和基于云的数据存储的内置支持。 有关详细列表，请参阅表[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
* **安全且合规**：通过 HTTPS 或 ExpressRoute 传输数据。 存在全局服务可确保数据永远不会离开地理边界。
* **通过使用 PolyBase 提供无与伦比的性能**：使用 Polybase 是将数据移到 Azure Synapse Analytics 的最高效方法。 使用临时 blob 功能，可以实现所有类型的数据存储（包括 Azure Blob 存储和 Data Lake Store）的高加载速度。 （默认情况下，Polybase 支持 Azure Blob 存储和 Azure Data Lake Store。）有关详细信息，请参阅[复制活动性能](copy-activity-performance.md)。

本文介绍如何使用复制数据工具将数据从 Azure SQL 数据库加载至 Azure Synapse Analytics。 可以遵循类似步骤，从其他类型的数据存储中复制数据。

> [!NOTE]
> 有关详细信息，请参阅[向/从 Azure Synapse Analytics 复制数据](connector-azure-sql-data-warehouse.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* Azure Synapse Analytics：此数据仓库保存从 SQL 数据库复制的数据。 如果没有 Azure Synapse Analytics，请参阅[创建 Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md) 中的说明。
* Azure SQL 数据库：本教程从 Azure SQL 数据库中的 Adventure Works LT 示例数据集中复制数据。 可以按照[在 Azure SQL 数据库中创建示例数据库](../azure-sql/database/single-database-create-quickstart.md)中的说明在 SQL 数据库中创建此示例数据库。
* Azure 存储帐户：Azure 存储用作大容量复制操作中的暂存 blob。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-account-create.md)中的说明。

## <a name="create-a-data-factory"></a>创建数据工厂

> [!NOTE]
> 如果希望使用现有 Synapse 工作区中的管道功能来加载数据，则可以跳过创建新数据工厂的步骤。  Azure Synapse 在其管道功能中嵌入了 Azure 数据工厂的功能。

1. 在左侧菜单中，选择“创建资源” > “数据 + 分析” > “数据工厂”：

2. 在“新建数据工厂”页上，为以下项提供值：

    * **名称**：输入“LoadSQLDWDemo”作为名称。 数据工厂的名称必须全局独一无二。 如果收到错误“数据工厂名称‘LoadSQLDWDemo’不可用”，请为数据工厂输入其他名称。 例如，可以使用名称 _**yourname**_**ADFTutorialDataFactory**。 请重试创建数据工厂。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。
    * **订阅**：选择要在其中创建数据工厂的 Azure 订阅。 
    * **资源组**：从下拉列表中选择现有资源组，或选择“新建”选项并输入资源组的名称。 若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。  
    * **版本**：选择“V2”。
    * **位置**：选择数据工厂的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储可以在其他位置和区域中。 这些数据存储包括 Azure Data Lake Store、Azure 存储、Azure SQL 数据库，等等。

3. 选择“创建”  。
4. 创建操作完成后，请转到数据工厂。 此时会看到“数据工厂”主页，如下图所示：

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Azure 数据工厂主页，其中包含“打开 Azure 数据工厂工作室”磁贴。":::

   在“打开 Azure 数据工厂工作室”磁贴上选择“打开”，以便在单独的标签页中启动“数据集成应用程序”。 

## <a name="load-data-into-azure-synapse-analytics"></a>将数据加载到 Azure Synapse Analytics 中

1. 在 Azure 数据工厂或 Azure Synapse 工作区的主页中，选择“引入”磁贴以启动复制数据工具。  然后选择“内置复制任务”。

2. 在“属性”页上，选择“任务类型”下的“内置复制任务”，然后选择“下一步”   。

    ![“属性”页](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

3. 在“源数据存储”页上，完成以下步骤：
    >[!TIP]
    >本教程使用“SQL 身份验证”作为源数据存储的身份验证类型，但你可以根据需要选择其他受支持的身份验证方法：“服务主体”和“托管标识”。 有关详细信息，请参阅[此文](./connector-azure-sql-database.md#linked-service-properties)中的相应部分。
    >为了安全地存储数据存储的机密，我们还建议使用 Azure Key Vault。 有关详细说明，请参阅[此文](./store-credentials-in-key-vault.md)。

    1. 选择“+ 新建连接”。

    1. 从库中选择“Azure SQL 数据库”，然后选择“继续” 。 可以在搜索框中键入“SQL”以筛选连接器。

        ![选择 Azure SQL DB](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)
    
    1. 在“新建连接（Azure SQL 数据库）”页上，从下拉列表中选择服务器名称和 DB 名称，指定用户名和密码。 选择“测试连接”以验证设置，然后选择“创建” 。

        ![配置 Azure SQL DB](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)
    
    1. 在“源数据存储”页上的“连接”部分中，选择新创建的连接作为源 。

    1. 在“源表”部分中，输入 SalesLT 以筛选表。  选中“(全选)”复选框以便对副本使用所有表，然后选择“下一步”。

    ![显示“源数据存储”页配置的屏幕截图。](./media/load-azure-sql-data-warehouse/source-data-store-page.png)

4. 在“应用筛选器”页中，指定你的设置或选择“下一步”。 选择此页上的“预览数据”按钮即可预览数据并查看输入数据的架构。 

    :::image type="content" source="./media/load-azure-sql-data-warehouse/apply-filter.png" alt-text="显示“应用筛选器”页的屏幕截图。":::

5. 在“目标数据存储”页上，完成以下步骤：
    >[!TIP]
    >本教程使用“SQL 身份验证”作为目标数据存储的身份验证类型，但你可以根据需要选择其他受支持的身份验证方法：“服务主体”和“托管标识”。 有关详细信息，请参阅[此文](./connector-azure-sql-data-warehouse.md#linked-service-properties)中的相应部分。
    >为了安全地存储数据存储的机密，我们还建议使用 Azure Key Vault。 有关详细说明，请参阅[此文](./store-credentials-in-key-vault.md)。

    1. 选择“+ 新建连接”添加一个连接。

    1. 从库中选择“Azure Synapse Analytics”，然后选择“继续” 。

        ![选择 Azure Synapse Analytics](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    1. 在“新建连接 (Azure Synapse Analytics)”页上，从下拉列表中选择服务器名称和 DB 名称，指定用户名和密码。 选择“测试连接”以验证设置，然后选择“创建” 。

        ![配置 Azure Synapse Analytics](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    1. 在“目标数据存储”上的“连接”部分中选择新创建的连接作为接收器 。

6. 在“表映射”选项中查看内容并选择“下一步”。  此时会显示智能表映射。 源表已根据表名映射到目标表。 如果目标中不存在表，则默认情况下，服务将创建一个具有相同名称的目标表。 还可以将源表映射到现有目标表。

   ![显示“目标数据存储”页配置的屏幕截图。](./media/load-azure-sql-data-warehouse/destination-data-store-page.png)

1. 在“列映射”页中，查看内容并选择“下一步”。 智能表映射基于列名。 如果自动创建的表的服务、源和目标存储之间存在不兼容时，可能发生数据类型转换。 如果在源列与目标列之间进行不受支持的数据类型转换，会显示错误消息以及相应的表。

    ![“列映射”页](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. 在“设置”页上，完成以下步骤：

    1. 为“任务名称”字段指定 CopyFromSQLToSQLDW。 
    1. 在“暂存设置”部分，选择“+ 新建”，新建临时存储 。 该存储用于在通过 PolyBase 将数据加载至 Azure Synapse Analytics 前暂存数据。 复制完成后，会自动清除 Azure Blob 存储中的临时数据。

    1. 在“新建链接服务”页中，选择你的存储帐户，然后选择“创建”以部署链接服务。 

    1. 取消选择“使用类型默认值”选项，然后选择“下一步” 。

    ![配置 PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

8. 在“摘要”页中检查设置，然后选择“下一步”。

9. 在“部署”页中，选择“监视”可以监视管道（任务）   。 

    :::image type="content" source="./media/load-azure-sql-data-warehouse/deployment-complete-page.png" alt-text="显示了“部署”页的屏幕截图。":::
 
10. 请注意，界面中已自动选择左侧的“监视”选项卡。  管道运行成功完成后，在“管道名称”列下选择“CopyFromSQLToSQLDW”链接即可查看活动运行详细信息或重新运行该管道。 

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)
    :::image type="content" source="./media/load-azure-sql-data-warehouse/pipeline-monitoring.png" alt-text="监视管道运行":::    

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
    :::image type="content" source="./media/load-azure-sql-data-warehouse/pipeline-monitoring-synapse.png" alt-text="监视管道运行":::   

--- 

12. 若要切换回到管道运行视图，请选择顶部的“所有管道运行”链接。 选择“刷新”可刷新列表。

    ![监视活动运行](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. 若要监视每个复制活动的执行详情，请在活动运行视图中选择“活动名称”下的“详细信息”链接（眼镜图标）。  可以监视详细信息，例如，从源复制到接收器的数据量、吞吐量、执行步骤以及相应的持续时间和使用的配置。

    ![首次监视活动运行详细信息](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png)

    ![第二次监视活动运行详细信息](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png)

## <a name="next-steps"></a>后续步骤

请转至下列文章，了解 Azure Synapse Analytics 支持：

> [!div class="nextstepaction"]
>[Azure Synapse Analytics 连接器](connector-azure-sql-data-warehouse.md)