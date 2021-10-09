---
title: 使用 Azure SQL 托管实例代理运行 SSIS 包
description: 了解如何使用 Azure SQL 托管实例代理运行 SSIS 包。
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.author: lle
author: lrtoyou1223
ms.date: 04/14/2020
ms.openlocfilehash: b16cc04837fef57d5510d9c34be94f76dd87033d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730758"
---
# <a name="run-ssis-packages-by-using-azure-sql-managed-instance-agent"></a>使用 Azure SQL 托管实例代理运行 SSIS 包

本文介绍如何使用 Azure SQL 托管实例代理运行 SQL Server Integration Services (SSIS) 包。 此功能的行为类似于在本地环境中通过 SQL Server 代理计划 SSIS 包。

使用此功能可以运行存储在以下位置的 SSIS 包：SQL 托管实例中的 SSISDB、文件系统（例如 Azure 文件存储），或 Azure-SSIS 集成运行时包存储。

## <a name="prerequisites"></a>先决条件

若要使用此功能，请[下载](/sql/ssms/download-sql-server-management-studio-ssms)并安装最新版 SQL Server Management Studio (SSMS)。 版本支持详细信息如下所示：

- 若要运行 SSISDB 或文件系统中的包，请安装 SSMS 版本 18.5 或更高版本。
- 若要运行包存储中的包，请安装 SSMS 版本 18.6 或更高版本。

还需要在 Azure 数据工厂中[预配 Azure-SSIS 集成运行时](./tutorial-deploy-ssis-packages-azure.md)。 它使用 SQL 托管实例作为终结点服务器。

## <a name="run-an-ssis-package-in-ssisdb"></a>在 SSISDB 中运行 SSIS 包

在此过程中，将使用 SQL 托管实例代理来调用存储在 SSISDB 中的 SSIS 包。

1. 在最新版 SSMS 中，连接到 SQL 托管实例。
1. 创建新的代理作业和新的作业步骤。 在“SQL Server 代理”下，右键单击“作业”文件夹，然后选择“新建作业”  。

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png" alt-text="用于创建新代理作业的选项":::

1. 在“新建作业步骤”页上，选择“SQL Server Integration Services 包”作为类型 。

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png" alt-text="用于创建新 SSIS 作业步骤的选项":::

1. 在“包”选项卡上，选择“SSIS 目录”作为包位置 。
1. 由于 SSISDB 位于 SQL 托管实例中，因此不需要指定身份验证。
1. 指定 SSISDB 中的某个 SSIS 包。

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png" alt-text="“包”选项卡，其中包含包源类型选项":::

1. 在“配置”选项卡上，可执行以下操作：
  
   - 在“参数”下指定参数值。
   - 替代“连接管理器”下的值。
   - 替代该属性，然后在“高级”下选择日志记录级别。

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png" alt-text="“配置”选项卡，其中包含包源类型选项":::

1. 选择“确定”以保存代理作业配置。
1. 启动代理作业以运行 SSIS 包。

## <a name="run-an-ssis-package-in-the-file-system"></a>在文件系统中运行 SSIS 包

在此过程中，将使用 SQL 托管实例代理来运行存储在文件系统中的 SSIS 包。

1. 在最新版 SSMS 中，连接到 SQL 托管实例。
1. 创建新的代理作业和新的作业步骤。 在“SQL Server 代理”下，右键单击“作业”文件夹，然后选择“新建作业”  。

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png" alt-text="用于创建新代理作业的选项":::

1. 在“新建作业步骤”页上，选择“SQL Server Integration Services 包”作为类型 。

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png" alt-text="用于创建新 SSIS 作业步骤的选项":::

1. 在“包”选项卡上：

   1. 对于“包位置”，选择“文件系统” 。

   1. 对于“文件源类型”：

      - 如果包是上传到 Azure 文件存储，请选择“Azure 文件共享”。

        :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png" alt-text="文件源类型的选项":::

        包路径为 `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`。

        在“包文件访问凭据”下，输入 Azure 文件帐户名和帐户密钥，用于访问 Azure 文件。 域设置为 Azure。

      - 如果包是上传到网络共享，请选择“网络共享”。

        包路径是带有 .dtsx 扩展名的包文件的 UNC 路径。

        输入相应的域、用户名和密码，用于访问网络共享包文件。
   1. 如果包文件是使用密码加密的，请选择“加密密码”并输入密码。
1. 在“配置”选项卡中，如果需要通过配置文件来运行 SSIS 包，请输入配置文件路径。
   如果将配置存储在 Azure 文件存储中，则其配置路径为 `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`。
1. 在“执行选项”选项卡中，可以选择是要使用 Windows 身份验证还是 32 位运行时来运行 SSIS 包  。
1. 在“日志记录”选项卡中，可以选择日志记录路径和相应的日志记录访问凭据来存储日志文件。 
   默认情况下，日志记录路径与包文件夹路径相同，日志记录访问凭据与包访问凭据相同。
   如果将日志存储在 Azure 文件存储中，则日志记录路径为 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`。
1. 在“设置值”选项卡上，可以输入“属性路径”和“值”来替代包属性。

   例如，若要替代用户变量的值，请按以下格式输入其路径：`\Package.Variables[User::<variable name>].Value`。
1. 选择“确定”以保存代理作业配置。
1. 启动代理作业以运行 SSIS 包。

## <a name="run-an-ssis-package-in-the-package-store"></a>运行包存储中的 SSIS 包

在此过程中，将使用 SQL 托管实例代理来运行存储在 Azure-SSIS IR 包存储中的 SSIS 包。

1. 在最新版 SSMS 中，连接到 SQL 托管实例。
1. 创建新的代理作业和新的作业步骤。 在“SQL Server 代理”下，右键单击“作业”文件夹，然后选择“新建作业”  。

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png" alt-text="用于创建新代理作业的选项":::

1. 在“新建作业步骤”页上，选择“SQL Server Integration Services 包”作为类型 。

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png" alt-text="用于创建新 SSIS 作业步骤的选项":::

1. 在“包”选项卡上：

   1. 对于“包位置”，选择“包存储” 。

   1. 对于“包路径”：

      包路径为 `<package store name>\<folder name>\<package name>`。

      :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-package-store.png" alt-text="包存储类型的选项":::

   1. 如果包文件是使用密码加密的，请选择“加密密码”并输入密码。
1. 在“配置”选项卡中，如果需要通过配置文件来运行 SSIS 包，请输入配置文件路径。
   如果将配置存储在 Azure 文件存储中，则其配置路径为 `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`。
1. 在“执行选项”选项卡中，可以选择是要使用 Windows 身份验证还是 32 位运行时来运行 SSIS 包  。
1. 在“日志记录”选项卡中，可以选择日志记录路径和相应的日志记录访问凭据来存储日志文件。
   默认情况下，日志记录路径与包文件夹路径相同，日志记录访问凭据与包访问凭据相同。
   如果将日志存储在 Azure 文件存储中，则日志记录路径为 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`。
1. 在“设置值”选项卡上，可以输入“属性路径”和“值”来替代包属性。

   例如，若要替代用户变量的值，请按以下格式输入其路径：`\Package.Variables[User::<variable name>].Value`。
1. 选择“确定”以保存代理作业配置。
1. 启动代理作业以运行 SSIS 包。

## <a name="cancel-ssis-package-execution"></a>取消 SSIS 包执行

若要从 SQL 托管实例代理作业中取消包执行，请执行以下步骤，而不是直接停止代理作业：

1. 从 msdb.dbo.sysjobs 中找到 SQL 代理的 jobId 。
1. 使用以下查询，基于作业 ID 查找相应的 SSIS executionId：
   ```sql
   select * from '{table for job execution}' where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
   如果 SSIS 包位于 SSISDB 中，则使用 ssisdb.internal.execution_parameter_values 作为作业执行表。 如果 SSIS 包位于文件系统中，则使用 ssisdb.internal.execution_parameter_values_noncatalog。
1. 右键单击 SSISDB 目录，然后选择“活动操作”。

   :::image type="content" source="./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png" alt-text="SSISDB 目录的快捷菜单上的“活动操作”":::&quot;&quot;

1. 基于 executionId 停止相应的操作。

## <a name="next-steps"></a>后续步骤
还可以使用 Azure 数据工厂来计划 SSIS 包。 有关分步说明，请参阅 [Azure 数据工厂事件触发器](how-to-create-event-trigger.md)。