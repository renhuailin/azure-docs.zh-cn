---
title: Oracle 到 Azure 虚拟机上的 SQL Server：迁移指南
description: 本指南讲解了如何使用适用于 Oracle 的 SQL Server 迁移助手将 Oracle 架构迁移到 Azure 虚拟机上的 SQL Server。
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 11/06/2020
ms.openlocfilehash: 94239de790a23d2b300e52567fd456a012217928
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734320"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-virtual-machines"></a>迁移指南：Oracle 到 Azure 虚拟机上的 SQL Server
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

本指南讲解了如何使用适用于 Oracle 的 SQL Server 迁移助手将 Oracle 架构迁移到 Azure 虚拟机上的 SQL Server。

有关其他迁移指南，请参阅[数据库迁移](/data-migration)。

## <a name="prerequisites"></a>先决条件

若要将 Oracle 架构迁移到 Azure 虚拟机上的 SQL Server，需要：

- 一个受支持的源环境。
- [适用于 Oracle 的 SQL Server 迁移助手 (SSMA)](https://www.microsoft.com/download/details.aspx?id=54258)。
- 一个目标 [SQL Server VM](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md)。
- 对[适用于 Oracle 的 SSMA](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) 和[提供程序](/sql/ssma/oracle/connect-to-oracle-oracletosql)的必需权限。
- 用于访问源和目标的连接和足够权限。


## <a name="pre-migration"></a>预迁移

若要准备迁移到云，请验证你的源环境是否受支持，以及你是否满足各项先决条件。 这样做有助于确保高效且成功地完成迁移。

这部分过程涉及：
- 清点需要迁移的数据库。
- 评估这些数据库是否存在潜在的迁移问题或障碍。
- 解决你发现的任何问题。

### <a name="discover"></a>发现

使用 [MAP 工具包](https://go.microsoft.com/fwlink/?LinkID=316883)识别现有数据源，并详细了解你的业务使用的功能。 这样做可以更好地了解迁移并帮助你规划迁移。 此过程涉及扫描网络以识别你的组织的 Oracle 实例以及你使用的版本和功能。

若要使用 MAP 工具包进行清单扫描，请执行以下步骤：


1. 打开 [MAP 工具包](https://go.microsoft.com/fwlink/?LinkID=316883)。


1. 选择“创建/选择数据库”：

   ![屏幕截图，显示“创建/选择数据库”选项。](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. 选择“创建清单数据库”。 输入新清单数据库的名称，提供简短说明，然后选择“确定”

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="屏幕截图，显示用于创建清单数据库的界面。":::

1. 选择“收集清单数据”，打开清单和评估向导： 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="屏幕截图，显示“收集清单数据”链接。":::


1. 在清单和评估向导中选择“Oracle”，然后选择“下一步”  ：

   ![屏幕截图，显示清单和评估向导的“清单方案”页。](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. 选择最适合你的业务需求和环境的计算机搜索选项，然后选择“下一步”：

   ![屏幕截图，显示清单和评估向导的“发现方法”页。](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. 为要浏览的系统输入凭据或创建新凭据，然后选择“下一步”：

    ![屏幕截图，显示清单和评估向导的“所有计算机凭据”页。](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)


1. 设置凭据的顺序，然后选择“下一步”： 

   ![屏幕截图，显示清单和评估向导的“凭据顺序”页。](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  


1. 为要发现的每台计算机输入凭据。 可对每台计算机/机器使用唯一凭据，也可使用“所有计算机凭据”列表。  

   ![屏幕截图，显示清单和评估向导的“指定计算机和凭据”页。](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. 验证你的选择，然后选择“完成”：

   ![屏幕截图，显示清单和评估向导的“摘要”页。](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)


1. 扫描完成后，查看“数据收集”摘要。 扫描可能需要几分钟时间，具体取决于数据库的数量。 完成操作后，选择“关闭”：

   ![屏幕截图，显示“数据收集”摘要。](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. 选择“选项”以生成有关 Oracle 评估和数据库详细信息的报表。 选择两个选项（每次一个）来生成报表。


### <a name="assess"></a>评估

确定数据源后，使用[适用于 Oracle 的 SQL Server 迁移助手](https://www.microsoft.com/download/details.aspx?id=54258)评估要迁移到 SQL Server VM 的 Oracle 实例。 此助手将帮助你了解源数据库和目标数据库之间的差距。 你可以查看数据库对象和数据、评估数据库是否适合迁移、将数据库对象迁移到 SQL Server，然后将数据迁移到 SQL Server。

要创建评估，请执行以下步骤：


1. 打开[适用于 Oracle 的 SQL Server 迁移助手](https://www.microsoft.com/download/details.aspx?id=54258)。
1. 在“文件”菜单中，选择“新建项目”。
1. 为你的项目提供一个项目名称和位置，然后从列表中选择一个 SQL Server 迁移目标。 选择“确定”：

   ![屏幕截图，显示“新建项目”对话框。](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)


1. 选择“连接到 Oracle”。 在“连接到 Oracle”对话框中，输入 Oracle 连接的值：

   ![屏幕截图，显示“连接到 Oracle”对话框。](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   选择要迁移的 Oracle 架构：

   ![屏幕截图，显示可以迁移的 Oracle 架构的列表。](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)


1. 在“Oracle 元数据资源管理器”中，右键单击要迁移的 Oracle 架构，然后选择“创建报表” 。 这样做将生成一个 HTML 报表。 或者选择数据库，然后在顶部菜单中选择“创建报表”。

   ![屏幕截图，显示如何创建报表。](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. 查看包含转换统计信息、错误和警告的 HTML 报表。 分析报表以了解转换问题和解决方法。

    另外，还可以在 Excel 中打开报表以获取 Oracle 对象的清单，以及完成架构转换所需的工作量。 报表的默认位置是 SSMAProjects 中的报表文件夹。

   例如： `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`


   ![屏幕截图，显示一个转换报表。](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)


### <a name="validate-data-types"></a>验证数据类型

验证默认的数据类型映射，并根据需要对其进行更改（如有必要）。 为此，请执行下列步骤：


1. 在“工具”菜单中，选择“项目设置” 。
1. 选择“类型映射”选项卡。

   ![屏幕截图，显示“类型映射”选项卡。](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. 通过在“Oracle 元数据资源管理器”中选择表，可以更改每个表的类型映射。

### <a name="convert-the-schema"></a>转换架构

要转换架构，请执行以下步骤：

1. （可选）若要转换动态或即席查询，请右键单击节点，然后选择“添加语句”。

1. 在顶部菜单中选择“连接到 SQL Server”。
     1. 输入 Azure VM 上的 SQL Server 实例的连接详细信息。
     1. 从列表中选择你的目标数据库，或者提供一个新名称。 如果你提供新名称，则会在目标服务器上创建一个数据库。
     1. 提供身份验证详细信息。
     1. 选择“连接”  。


   ![屏幕截图，显示如何连接到 SQL Server。](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. 在“Oracle 元数据资源管理器”中，右键单击 Oracle 架构，然后选择“转换架构” 。 也可以在顶部菜单中选择“转换架构”：

   ![屏幕截图，显示如何转换架构。](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)


1. 在架构转换完成后，检查转换后的对象，将它们与原始对象进行比较，以查明潜在问题。 根据建议解决任何问题：

   ![屏幕截图，显示两个架构的对比。](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   将转换后的 Transact-SQL 文本与原始存储过程进行比较，并查看建议：

   ![屏幕截图，显示 Transact-SQL、存储过程和警告。](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   可在本地保存此项目供离线架构修正练习使用。 为此，请在“文件”菜单上选择“保存项目”。 如果将项目保存在本地，则可以在将架构发布到 SQL Server 之前，先对源和目标架构进行脱机评估并修正。

1. 在“输出”窗格中选择“查看结果”，然后在“错误列表”窗格中查看错误。  
1. 在本地保存该项目以进行脱机架构修正练习。 在“文件”菜单中，选择“保存项目” 。 这样，你就有机会在将架构发布到 Azure 虚拟机上的 SQL Server 之前，先对源和目标架构进行脱机评估并修正。


## <a name="migrate"></a>Migrate

在满足必需的先决条件并完成与预迁移阶段相关的任务后，就可以开始架构和数据迁移了。 迁移涉及两个步骤：发布架构和迁移数据。


要发布架构并迁移数据，请执行以下步骤：

1. 发布架构：在 SQL Server 元数据资源管理器中右键单击数据库，然后选择“与数据库同步”。  这样做会将 Oracle 架构发布到在 Azure 虚拟机上的 SQL Server。

   ![屏幕截图，显示“与数据库同步”命令。](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   查看源项目与目标之间的映射：

   ![屏幕截图，显示同步状态。](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)



1. 迁移数据：在“Oracle 元数据资源管理器”中右键单击要迁移的数据库或对象，然后选择“迁移数据”。  或者选择“迁移数据”选项卡。若要迁移整个数据库的数据，请选中数据库名称旁边的复选框。 若要从单个表中迁移数据，请展开数据库，展开“表”，然后选中表旁边的复选框。 若要忽略单个表中的数据，请清除对应的复选框。

   ![屏幕截图，显示“迁移数据”命令。](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. 在对话框中提供 Oracle 以及 Azure 虚拟机上的 SQL Server 的连接详细信息。
1. 迁移完成后，查看“数据迁移报表”：

    ![屏幕截图，显示数据迁移报表。](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. 使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 连接到 Azure 虚拟机实例上的 SQL Server。 通过查看数据和架构来验证迁移：


   ![屏幕截图，显示 SSMA 中的 SQL Server 实例。](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)

你可以使用 SQL Server Integration Services (SSIS) 来迁移数据，而不是使用 SSMA。 若要了解更多信息，请参阅以下文章：
- [SQL Server Integration Services](/sql/integration-services/sql-server-integration-services) 一文。
- [使用 SSIS 移动 Azure 数据和混合数据](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)白皮书。


## <a name="post-migration"></a>迁移后

完成迁移阶段后，需要完成一系列的迁移后任务，以确保一切都能尽量顺畅高效地运行。

### <a name="remediate-applications"></a>修正应用程序

将数据迁移到目标环境后，以前使用源的所有应用程序都需要开始使用目标。 进行这些更改可能需要更改应用程序。

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) 是 Visual Studio Code 的一个扩展。 它可以分析 Java 源代码并检测数据访问 API 调用和查询。 该工具包提供了一个单窗格视图，其中显示了为新数据库后端提供支持所需满足的要求。 有关详细信息，请参阅[从 Oracle 迁移 Java 应用程序](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727)。

### <a name="perform-tests"></a>执行测试

若要测试数据库迁移，请完成以下活动：

1. **开发验证测试**。 要测试数据库迁移，需要使用 SQL 查询。 创建对源数据库和目标数据库都运行的验证查询。 验证查询应当涵盖已定义的范围。

2. 设置测试环境。 测试环境应包含源数据库和目标数据库的副本。 请确保隔离测试环境。

3. **运行验证测试**。 针对源和目标运行验证测试，然后分析结果。

4. **运行性能测试**。 针对源和目标运行性能测试，然后分析和比较结果。

### <a name="validate-migrated-objects"></a>验证迁移的对象

适用于 Oracle 的 Microsoft SQL Server 迁移助手测试程序（SSMA 测试程序）可用于测试迁移的数据库对象。 SSMA 测试程序用于验证转换后对象的行为方式是否相同。

#### <a name="create-test-case"></a>创建测试用例

1. 打开 SSMA for Oracle，选择“测试程序”，然后选择“新建测试用例” 。

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/ssma-tester-new.png" alt-text="显示“新建测试用例”的屏幕截图。":::

1. 在“测试用例”向导上，提供以下信息：

   **名称**：输入名称以标识测试用例。

   **创建日期**：当天的当前日期，自动定义。

   **上次修改日期**：自动填充，不应更改。

   **说明**：输入任何其他信息以标识测试用例的用途。

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-init-test-case.png" alt-text="显示初始化测试用例步骤的屏幕截图。":::

1. 从位于左侧的 Oracle 对象树中选择属于测试用例的对象。

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-select-configure-objects.png" alt-text="显示选择和配置对象步骤的屏幕截图。":::

   在此示例中，选择了存储过程 `ADD_REGION` 和表 `REGION`。

   若要了解详细信息，请参阅[选择并配置要测试的对象](/sql/ssma/oracle/selecting-and-configuring-objects-to-test-oracletosql)。

1. 接下来，从左侧窗口中的 Oracle 对象树中选择表、外键和其他依赖对象。

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-select-configure-affected.png" alt-text="显示选择和配置受影响对象步骤的屏幕截图。":::

   若要了解详细信息，请参阅[选择并配置受影响的对象。](/sql/ssma/oracle/selecting-and-configuring-affected-objects-oracletosql)

1. 查看对象的评估顺序。 单击网格中的按钮更改顺序…

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/test-call-ordering.png" alt-text="显示测试对象执行排序步骤的屏幕截图。":::

1. 查看前面步骤中提供的信息来完成测试用例。 根据测试方案配置测试执行选项。

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-finalize-case.png" alt-text="显示完成对象步骤的屏幕截图。":::

   有关测试用例设置的详细信息，请参阅[完成测试用例准备](/sql/ssma/oracle/finishing-test-case-preparation-oracletosql)

1. 单击“完成”以创建测试用例。

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-test-repo.png" alt-text="显示测试存储库步骤的屏幕截图。":::

#### <a name="run-test-case"></a>运行测试用例

当 SSMA 测试程序运行测试用例时，测试引擎会执行为测试选择的对象并生成验证报告。

1. 从测试存储库中选择测试用例，然后单击“运行”。
   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-repo-run.png" alt-text="显示查看测试存储库的屏幕截图。":::

1. 查看启动测试用例，然后单击“运行”。

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-run-test-case.png" alt-text="显示启动测试用例步骤的屏幕截图。":::

1. 接下来，提供 Oracle 源凭据。 输入凭据后单击“连接”。

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-oracle-connect.png" alt-text="显示连接到 Oracle 源的步骤的屏幕截图。":::

1. 提供目标 SQL Server 凭据，然后单击“连接”。

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-sqlservervm-connect.png" alt-text="显示连接到 SQL 目标的步骤的屏幕截图。":::

   成功后，测试用例会进入初始化阶段。

1. 实时进度栏显示测试运行的执行状态。

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-run-status.png" alt-text="显示测试程序测试进度的屏幕截图。":::

1. 测试完成后查看报表。 该报表提供统计信息、测试运行期间的所有错误和详细信息报告。

   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-test-result.png" alt-text="显示示例测试程序测试报告的屏幕截图":::

7. 单击详细信息以获取详细信息。

   正数据验证示例。
   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-test-success.png" alt-text="显示示例测试程序成功报告的屏幕截图。":::

   失败的数据验证示例。
   :::image type="content" source="./media/oracle-to-sql-on-azure-vm-guide/tester-test-failed.png" alt-text="显示测试程序失败报告的屏幕截图。":::

### <a name="optimize"></a>优化

迁移后阶段对于协调任何数据准确性问题和验证完整性至关重要。 这对于解决工作负荷的性能问题也至关重要。

> [!Note]
> 若要详细了解这些问题以及用于缓解问题的具体步骤，请参阅[迁移后验证和优化指南](/sql/relational-databases/post-migration-validation-and-optimization-guide)。


## <a name="migration-resources"></a>迁移资源

在完成此迁移方案时如需更多帮助，请参阅以下资源，它们是为了支持实际迁移项目而创作的。

| 标题/链接                                                                                                                                          | **说明**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [数据工作负载评估模型和工具](https://www.microsoft.com/download/details.aspx?id=103130) | 此工具为给定工作负荷提供建议的“最适合”目标平台、云就绪性和应用程序/数据库修正级别。 它提供简单的一键式计算和报表生成功能，通过提供统一的自动化目标平台决策过程，帮助加速大规模评估。                                                          |
| [Oracle 清单脚本项目](https://www.microsoft.com/download/details.aspx?id=103121)                 | 此资产包含一个 PL/SQL 查询，该查询以 Oracle 系统表作为查询目标，并按架构类型、对象类型和状态提供对象计数。 它还提供每个架构中“原始数据”和表大小的粗略估算，结果以 CSV 格式存储。                                                                                                               |
| [自动进行 SSMA Oracle 评估收集和整合](https://www.microsoft.com/download/details.aspx?id=103120)                                             | 这组资源使用 .csv 文件作为输入（项目文件夹中的 sources.csv），生成在控制台模式下运行 SSMA 评估所需的 XML 文件。 可以通过清点现有 Oracle 实例来提供 source.csv 文件。 输出文件为 AssessmentReportGeneration_source_1.xml、ServersConnectionFile.xml 和 VariableValueFile.xml。|
| [迁移 Oracle 数据库时的 SSMA 问题和可能的修正措施](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | 使用 Oracle 时，可以在 WHERE 子句中分配非标量条件。 SQL Server 不支持这种类型的条件。 因此，适用于 Oracle 的 SSMA 不会转换在 WHERE 子句中具有非标量条件的查询， 而是会生成错误 O2SS0001。 此白皮书提供了有关该问题及其解决方法的详细信息。          |
| [Oracle 到 SQL Server 迁移手册](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | 此文档重点介绍在将 Oracle 架构迁移到最新版本的 SQL Server 时涉及的任务。 如果迁移要求更改特性/功能，则需要仔细考虑对使用数据库的应用程序所做的每项更改可能造成的影响。                                                     |
|[从 Oracle 到 SQL Server - 数据库比较实用工具](https://www.microsoft.com/download/details.aspx?id=103016)|SSMA for Oracle 测试工具是推荐用于自动验证数据库对象转换和数据迁移的工具，它是数据库比较功能的超集。<br /><br />如果要查找备用的数据验证选项，则可使用数据库比较实用工具沿所有或所选表、行和列中的行或列级别比较数据。|


数据 SQL 工程团队开发了这些资源。 此团队的核心章程是解锁和加速到 Microsoft Azure 数据平台的数据平台迁移项目的复杂现代化进程。


## <a name="next-steps"></a>后续步骤

- 若要检查适用于 SQL Server 的服务的可用性，请参阅 [Azure 全球基础结构中心](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)。

- 如需一整套 Microsoft 与第三方的服务和工具，以便在执行各种数据库和数据迁移方案及专门任务时获取帮助，请查看[数据迁移服务和工具](../../../dms/dms-tools-matrix.md)。

- 若要详细了解 Azure SQL，请参阅：
   - [部署选项](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure 虚拟机中的 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure 总拥有成本计算器](https://azure.microsoft.com/pricing/tco/calculator/)


- 有关云迁移的框架和采用周期的详细信息，请参阅：
   -  [适用于 Azure 的云采用框架](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [为迁移到 Azure 的工作负荷计算成本和调整大小的最佳做法](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 有关许可的信息，请参阅：
   - [通过 Azure 混合权益自带许可](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [获得对 SQL Server 2008 和 SQL Server 2008 R2 的免费外延支持](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- 若要评估应用程序访问层，请使用 [Data Access Migration Toolkit 预览版](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)。
- 若要详细了解如何执行数据访问层 A/B 测试，请参阅[数据库实验助手概述](/sql/dea/database-experimentation-assistant-overview)。
