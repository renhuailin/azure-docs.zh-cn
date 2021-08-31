---
title: 从 Oracle 迁移到 Azure SQL 托管实例：迁移指南
description: 本指南介绍如何使用适用于 Oracle 的 SQL Server 迁移助手将 Oracle 架构迁移到 Azure SQL 托管实例。
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 11/06/2020
ms.openlocfilehash: 7dff98002d92c6214a6e88f0bb20ef216329e127
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745800"
---
# <a name="migration-guide-oracle-to-azure-sql-managed-instance"></a>迁移指南：Oracle 到 Azure SQL 托管实例

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

 本指南介绍如何使用适用于 Oracle 的 SQL Server 迁移助手将 Oracle 构架迁移到 Azure SQL 托管实例。

有关其他迁移指南，请参阅 [Azure 数据库迁移指南](/data-migration)。

## <a name="prerequisites"></a>先决条件

开始将 Oracle 架构迁移到 SQL 托管实例之前：

- 验证源环境是否受支持。
- 下载 [SSMA for Oracle](https://www.microsoft.com/download/details.aspx?id=54258)。
- 具有 [SQL 托管实例](../../managed-instance/instance-create-quickstart.md)目标。
- 获取 [SSMA for Oracle 的必需权限](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql)和[提供程序](/sql/ssma/oracle/connect-to-oracle-oracletosql)。
 
## <a name="pre-migration"></a>预迁移

满足先决条件后，就已准备就绪，可以探索环境的拓扑并评估迁移的可行性了。 迁移过程的这一部分涉及到对需要迁移的数据库进行清点、评估这些数据库是否存在潜在的迁移问题或阻碍因素，然后处理你可能已发现的任何项目。

### <a name="assess"></a>评估

借助 SSMA for Oracle，可查看数据库对象和数据、评估数据库是否适合迁移、将数据库对象迁移到 SQL 托管实例，最后将数据迁移到数据库。

若要创建评估：

1. 打开 [SSMA for Oracle](https://www.microsoft.com/download/details.aspx?id=54258)。
1. 依次选择“文件”和“新建项目” 。
1. 输入项目名称和保存项目的位置。 然后从下拉列表中选择“Azure SQL 托管实例”作为迁移目标，再选择“确定” 。

   ![显示“新建项目”的屏幕截图。](./media/oracle-to-managed-instance-guide/new-project.png)

1. 选择“连接到 Oracle”。 在“连接到 Oracle”对话框中，输入 Oracle 连接详细信息的值。

   ![显示“连接到 Oracle”的屏幕截图。](./media/oracle-to-managed-instance-guide/connect-to-oracle.png)

1. 选择要迁移的 Oracle 架构。

   ![显示选择 Oracle 架构的屏幕截图。](./media/oracle-to-managed-instance-guide/select-schema.png)

1. 在 Oracle 元数据资源管理器中，右键单击要迁移的 Oracle 架构，然后选择“创建报表”来生成 HTML 报表 。 或者，可选择一个数据库，然后选择“创建报表”选项卡。

   ![显示“创建报表”的屏幕截图。](./media/oracle-to-managed-instance-guide/create-report.png)

1. 查看 HTML 报表，了解转换统计信息以及任何错误或警告。 另外，还可以在 Excel 中打开报表以获取 Oracle 对象的清单，以及执行架构转换所需的工作量。 报表的默认位置在 SSMAProjects 内的报表文件夹中。

   有关示例，请参阅 `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`。

   ![显示“评估报表”的屏幕截图。](./media/oracle-to-managed-instance-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>验证数据类型

验证默认的数据类型映射，并根据需要对其进行更改（如有必要）。 为此，请执行下列步骤：

1. 在 SSMA for Oracle 中，选择“工具”，然后选择“项目设置” 。
1. 选择“类型映射”选项卡。

   ![显示“类型映射”的屏幕截图。](./media/oracle-to-managed-instance-guide/type-mappings.png)

1. 通过在“Oracle 元数据资源管理器”中选择表，可以更改每个表的类型映射。

### <a name="convert-the-schema"></a>转换架构

若要转换架构：

1. （可选）向语句中添加动态或临时查询。 右键单击节点，然后选择“添加语句”。
1. 选择“连接到 Azure SQL 托管实例”选项卡。
    1. 输入连接详细信息，连接 SQL 数据库托管实例中的数据库。
    1. 从下拉菜单中选择目标数据库，或者输入新名称；如果是输入新名称，则将在目标服务器上创建一个数据库。
    1. 输入身份验证详细信息，然后选择“连接”。

    ![显示“连接到 Azure SQL 托管实例”的屏幕截图。](./media/oracle-to-managed-instance-guide/connect-to-sql-managed-instance.png)

1. 在 Oracle 元数据资源管理器中，右键单击 Oracle 架构，然后选择“转换架构” 。 或者，可以选择架构，然后选择“转换架构”选项卡。

   ![显示“转换架构”的屏幕截图。](./media/oracle-to-managed-instance-guide/convert-schema.png)

1. 转换完成后，查看转换后的对象并与原始对象进行比较，以识别潜在问题并根据建议解决这些问题。

   ![显示比较表建议的屏幕截图。](./media/oracle-to-managed-instance-guide/table-comparison.png)

1. 将转换后的 Transact-SQL 文本与原始代码进行比较，并查看建议。

   ![显示比较过程建议的屏幕截图。](./media/oracle-to-managed-instance-guide/procedure-comparison.png)

1. 在“输出”窗格中，选择“查看结果”，然后在“错误列表”窗格中查看错误 。
1. 在本地保存该项目以进行脱机架构修正练习。 在“文件”菜单中，选择“保存项目” 。 通过此步骤，你有机会在将架构发布到 SQL 托管数据库之前，先对源架构和目标架构进行离线评估并执行修正。

## <a name="migrate"></a>Migrate

完成对数据库的评估并解决任何分歧后，下一步就是运行迁移过程。 迁移涉及两个步骤：发布架构和迁移数据。

若要发布架构和迁移数据：

1. 发布架构：在 Azure SQL 托管实例元数据资源管理器中右键单击“数据库”节点中的数据库，然后选择“与数据库同步”  。

   ![显示“与数据库同步”的屏幕截图。](./media/oracle-to-managed-instance-guide/synchronize-with-database.png)
   

1. 检查源项目与目标之间的映射。

   ![显示“与数据库同步”检查的屏幕截图。](./media/oracle-to-managed-instance-guide/synchronize-with-database-review.png)

1. 迁移数据：在 Oracle 元数据资源管理器中右键单击要迁移的架构或对象，然后选择“迁移数据” 。 或者选择“迁移数据”选项卡。若要迁移整个数据库的数据，请选中数据库名称旁边的复选框。 若要从单个表中迁移数据，请展开数据库，展开“表”，然后选中表旁边的复选框。 若要忽略单个表中的数据，请清除对应的复选框。

   ![显示“迁移数据”的屏幕截图。](./media/oracle-to-managed-instance-guide/migrate-data.png)

1. 输入 Oracle 和 SQL 托管实例的连接详细信息。
1. 迁移完成后，查看数据迁移报表。

   ![显示数据迁移报表的屏幕截图。](./media/oracle-to-managed-instance-guide/data-migration-report.png)

1. 使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 连接到 SQL 托管实例服务的实例，并通过查看数据和架构来验证迁移。

   ![该屏幕截图显示 SSMA for Oracle 中的验证。](./media/oracle-to-managed-instance-guide/validate-data.png)

或者，还可使用 SQL Server Integration Services 来执行迁移。 若要了解更多信息，请参阅以下文章：

- [SQL Server Integration Services 入门](/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services for Azure 和混合数据移动](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>迁移后

成功完成迁移阶段后，需要完成一系列的迁移后任务，以确保一切都能尽量顺畅高效地正常进行。

### <a name="remediate-applications"></a>修正应用程序

将数据迁移到目标环境后，以前使用源的所有应用程序都需要开始使用目标。 在某些情况下，需要更改应用程序才能完成此步骤。

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) 是 Visual Studio Code 的扩展，可用于分析 Java 源代码并检测数据访问 API 调用和查询。 该工具包提供了一个单窗格视图，其中显示了支持新数据库后端需要满足的要求。 有关详细信息，请参阅[从 Oracle 迁移 Java 应用程序](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727)博客文章。

### <a name="perform-tests"></a>执行测试

数据库迁移的测试方法包括以下活动：

1. **开发验证测试**：要测试数据库迁移，需要使用 SQL 查询。 必须创建针对源数据库和目标数据库运行的验证查询。 验证查询应涵盖已定义的范围。
2. **设置测试环境**：测试环境应包含源数据库和目标数据库的副本。 请确保隔离测试环境。
3. **运行验证测试**：针对源和目标运行验证测试，然后分析结果。
4. 运行性能测试：针对源和目标运行性能测试，然后分析和比较结果。

### <a name="validate-migrated-objects"></a>验证迁移的对象

适用于 Oracle 的 Microsoft SQL Server 迁移助手测试程序（SSMA 测试程序）可用于测试迁移的数据库对象。 SSMA 测试程序用于验证转换后对象的行为方式是否相同。

#### <a name="create-test-case"></a>创建测试用例

1. 打开 SSMA for Oracle，选择“测试程序”，然后选择“新建测试用例” 。

   :::image type="content" source="./media/oracle-to-managed-instance-guide/ssma-tester-new.png" alt-text="显示“新建测试用例”的屏幕截图。":::

1. 在“测试用例”向导上，提供以下信息：

   名称：输入名称以标识测试用例。

   创建日期：当天的当前日期，自动定义。

   上次修改日期：自动填充，不应更改。

   说明：输入任何其他信息以标识测试用例的用途。

   :::image type="content" source="./media/oracle-to-managed-instance-guide/tester-init-test-case.png" alt-text="显示初始化测试用例步骤的屏幕截图。":::

1. 从位于左侧的 Oracle 对象树中选择属于测试用例的对象。

   :::image type="content" source="./media/oracle-to-managed-instance-guide/tester-select-configure-objects.png" alt-text="显示选择和配置对象步骤的屏幕截图。":::

   在本示例中，选择了存储过程 `ADD_REGION` 和表 `REGION`。

   若要了解详细信息，请参阅[选择并配置要测试的对象](/sql/ssma/oracle/selecting-and-configuring-objects-to-test-oracletosql)。

1. 接下来，从左侧窗口中的 Oracle 对象树中选择表、外键和其他依赖对象。

   :::image type="content" source="./media/oracle-to-managed-instance-guide/tester-select-configure-affected.png" alt-text="显示选择和配置受影响对象步骤的屏幕截图。":::

    若要了解详细信息，请参阅[选择并配置受影响的对象。](/sql/ssma/oracle/selecting-and-configuring-affected-objects-oracletosql)

1. 查看对象的评估顺序。 单击网格中的按钮来更改顺序。

   :::image type="content" source="./media/oracle-to-managed-instance-guide/test-call-ordering.png" alt-text="显示测试对象执行排序步骤的屏幕截图。":::

1. 通过查看之前步骤中提供的信息来完成测试用例。基于测试方案配置测试执行选项。

   :::image type="content" source="./media//oracle-to-managed-instance-guide/tester-finalize-case.png" alt-text="显示完成对象步骤的屏幕截图。":::

   有关测试用例设置的详细信息，请参阅[完成测试用例准备](/sql/ssma/oracle/finishing-test-case-preparation-oracletosql)

1. 单击“完成”以创建测试用例。

   :::image type="content" source="./media/oracle-to-managed-instance-guide/tester-test-repo.png" alt-text="显示测试存储库步骤的屏幕截图。":::

#### <a name="run-test-case"></a>运行测试用例

当 SSMA 测试程序运行测试用例时，测试引擎会执行为测试选择的对象并生成验证报告。

1. 从测试存储库中选择测试用例，然后单击“运行”。

   :::image type="content" source="./media/oracle-to-managed-instance-guide/tester-repo-run.png" alt-text="显示查看测试存储库的屏幕截图。":::

1. 查看启动测试用例，然后单击“运行”。

   :::image type="content" source="./media/oracle-to-managed-instance-guide/tester-run-test-case.png" alt-text="显示启动测试用例步骤的屏幕截图。":::

1. 接下来，提供 Oracle 源凭据。 输入凭据后单击“连接”。

   :::image type="content" source="./media/oracle-to-managed-instance-guide/tester-oracle-connect.png" alt-text="显示连接到 Oracle 源的步骤的屏幕截图。":::

1. 提供目标 SQL Server 凭据，然后单击“连接”。

   :::image type="content" source="./media/oracle-to-managed-instance-guide/tester-sqlmi-connect.png" alt-text="显示连接到 SQL 目标的步骤的屏幕截图。":::

   成功后，测试用例会进入初始化阶段。

1. 实时进度栏显示测试运行的执行状态。

   :::image type="content" source="./media/oracle-to-managed-instance-guide/tester-run-status.png" alt-text="显示测试程序测试进度的屏幕截图。":::

1. 测试完成后查看报表。 该报表提供统计信息、测试运行期间的所有错误和详细信息报告。

   :::image type="content" source="./media/oracle-to-managed-instance-guide/tester-test-result.png" alt-text="显示示例测试程序测试报告的屏幕截图":::

1. 单击“详细信息”以获取详细信息。

   正数据验证示例。

   :::image type="content" source="./media/oracle-to-managed-instance-guide/tester-test-success.png" alt-text="显示示例测试程序成功报告的屏幕截图。":::

   失败的数据验证示例。

   :::image type="content" source="./media/oracle-to-managed-instance-guide/tester-test-failed.png" alt-text="显示测试程序失败报告的屏幕截图。":::

### <a name="optimize"></a>优化

迁移后阶段对于协调任何数据准确性问题、验证完整性以及解决工作负载的性能问题至关重要。

> [!NOTE]
> 有关这些问题及其缓解步骤的详细信息，请参阅[迁移后验证和优化指南](/sql/relational-databases/post-migration-validation-and-optimization-guide)。

## <a name="migration-assets"></a>迁移资产

若要获得完成此迁移方案的更多帮助，请参阅以下资源。 这些资源是为支持实际迁移项目而开发的。

| **标题/链接**                                                                                                                                          | **说明**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [数据工作负载评估模型和工具](https://www.microsoft.com/download/details.aspx?id=103130) | 此工具为给定工作负载提供建议的“最适合”目标平台、云就绪性和应用程序/数据库修正级别。 它提供简单的一键式计算和报表生成功能，通过提供统一的自动化目标平台决策过程，帮助加速大规模评估。                                                          |
| [Oracle 清单脚本项目](https://www.microsoft.com/download/details.aspx?id=103121)                 | 该资产包含一个 PL/SQL 查询，它会命中 Oracle 系统表，并按架构类型、对象类型和状态提供对象计数。 它还提供每个架构中“原始数据”和表大小的粗略估算，结果以 CSV 格式存储。                                                                                                               |
| [自动进行 SSMA Oracle 评估收集和整合](https://www.microsoft.com/download/details.aspx?id=103120)                                             | 这组资源使用 .csv 文件作为输入（项目文件夹中的 sources.csv），来生成在控制台模式下运行 SSMA 评估所需的 xml 文件。 source.csv 是客户根据现有 Oracle 实例的清单提供的。 输出文件为 AssessmentReportGeneration_source_1.xml、ServersConnectionFile.xml 和 VariableValueFile.xml。|
|[从 Oracle 到 SQL MI - 数据库比较实用工具](https://www.microsoft.com/download/details.aspx?id=103016)|SSMA for Oracle 测试工具是推荐用于自动验证数据库对象转换和数据迁移的工具，它是数据库比较功能的超集。<br /><br />如果要查找备用的数据验证选项，则可使用数据库比较实用工具沿所有或所选表、行和列中的行或列级别比较数据。|

数据 SQL 工程团队开发了这些资源。 此团队的核心章程是解锁和加速到 Microsoft 的 Azure 数据平台的数据平台迁移项目的复杂现代化进程。

## <a name="next-steps"></a>后续步骤

- 如需一整套 Microsoft 与第三方服务和工具，以便在执行各种数据库和数据迁移方案及专门任务时可为你提供帮助，请查看[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)。

- 要详细了解 SQL 托管实例，请参阅：
  - [Azure SQL 托管实例概述](../../managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure 总拥有成本 (TCO) 计算器](https://azure.microsoft.com/pricing/tco/calculator/)

- 有关云迁移的框架和采用周期的详细信息，请参阅：
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [为迁移到 Azure 的工作负载计算成本和调整大小的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- 有关视频内容，请参阅：
    - [迁移之旅及推荐用于执行评估和迁移的工具和服务的概述](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
