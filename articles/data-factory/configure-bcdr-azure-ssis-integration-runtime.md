---
title: 配置 Azure-SSIS 集成运行时以实现业务连续性和灾难恢复 (BCDR)
description: 本文介绍如何在 Azure 数据工厂中为 Azure SQL 数据库/托管实例故障转移组配置 Azure-SSIS 集成运行时，以实现业务连续性和灾难恢复 (BCDR)。
services: data-factory
ms.service: data-factory
ms.subservice: integration-services
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/05/2021
ms.openlocfilehash: 453f1db3e0f80a63c058c7e0ea21ab9282295de6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735420"
---
# <a name="configure-azure-ssis-integration-runtime-for-business-continuity-and-disaster-recovery-bcdr"></a>配置 Azure-SSIS 集成运行时以实现业务连续性和灾难恢复 (BCDR) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure 数据工厂 (ADF) 中的 Azure SQL 数据库/托管实例和 SQL Server Integration Services (SSIS) 可以组合为用于 SQL Server 迁移的所有平台即服务 (PaaS) 推荐解决方案。 你可以将 SSIS 项目部署到由 Azure SQL 数据库/托管实例托管的 SSIS 目录数据库 (SSISDB)，并在 ADF 的 Azure SSIS 集成运行时 (IR) 上运行 SSIS 包。

为实现业务连续性和灾难恢复 (BCDR)，可以使用[异地复制/故障转移组](../azure-sql/database/auto-failover-group-overview.md)来配置 Azure SQL 数据库/托管实例，其中，具有读写访问权限（主要角色）的主要 Azure 区域中的 SSISDB 会持续复制到具有只读访问权限（辅助角色）的次要区域。 当主要区域发生灾难时，将触发故障转移，主要和辅助 SSISDB 将在其中交换角色。

对于 BCDR，还可以配置与 Azure SQL 数据库/托管实例故障转移组同步运行的双重备用 Azure SSIS IR 对。 这允许你有一对在任何给定时间运行的 Azure-SSIS IR，只有一个可以访问主要 SSISDB 来提取和执行包，并写入包执行日志（主要角色），而另一个则只能对部署在其他位置的包执行相同操作，例如，在 Azure 文件存储中（辅助角色）。 发生 SSISDB 故障转移时，主要和辅助 Azure-SSIS IR 还将交换角色，如果两者都在运行，那么停机时间近乎为零。

本文介绍如何在 BCDR 的 Azure SQL 数据库/托管实例故障转移组中配置 Azure-SSIS IR。

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-database-failover-group"></a>使用 Azure SQL 数据库故障转移组配置双重备用 Azure-SSIS IR 对

若要配置与 Azure SQL 数据库故障转移组同步运行的双重备用 Azure-SSIS IR 对，请完成以下步骤。

1. 使用 Azure 门户/ADF UI，你可以创建一个带有主要 Azure SQL 数据库服务器的新 Azure-SSIS IR，以在主要区域中托管 SSISDB。 如果现有 Azure-SSIS IR 已附加到 Azure SQL 数据库服务器托管的 SSIDB，并且仍在运行，则需要先将其停止才能重新配置它。 这将是主要 Azure-SSIS IR。

   在“集成运行时设置”窗格的“部署设置”页上[选择使用 SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb)时，请选中“结合使用双重备用 Azure-SSIS Integration Runtime 对和 SSISDB 故障转移”复选框。 对于“双重备用对名称”，请输入一个名称以标识主要和辅助 Azure-SSIS IR 对。 完成主要 Azure-SSIS IR 的创建后，它将启动并附加到主要 SSISDB，该 SSISDB 将以你的身份创建，具有读写访问权限。 如果刚重新配置了它，则需要重新启动。

1. 使用 Azure 门户，可以检查是否已在主 Azure SQL 数据库服务器的“概述”页上创建了主要 SSISDB。 创建后，可以在“故障转移组”页上[为主要和辅助 Azure SQL 数据库服务器创建故障转移组，并向其添加 SSISDB](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#2---create-the-failover-group)。 创建故障转移组后，可以检查是否已在辅助 Azure SQL 数据库服务器的“概述”页上将主要 SSISDB 复制到具有只读访问权限的辅助 SSISDB。

1. 使用 Azure 门户/ADF UI，你可以通过辅助 Azure SQL 数据库服务器创建另一个 Azure-SSIS IR 来托管次要区域中的 SSISDB。 这将是你的辅助 Azure-SSIS IR。 对于完整的 BCDR，请确保还会在次要区域中创建它所依赖的所有资源，例如，用于存储自定义安装程序脚本/文件的 Azure 存储、用于业务流程/计划包执行的 ADF 等。

   在“集成运行时设置”窗格的“部署设置”页上[选择使用 SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb)时，请选中“结合使用双重备用 Azure-SSIS Integration Runtime 对和 SSISDB 故障转移”复选框。 对于“双重备用对名称”，请输入相同名称以标识主要和辅助 Azure-SSIS IR 对。 完成辅助 Azure-SSIS IR 的创建后，它将启动并附加到辅助 SSISDB。

1. 如果希望在发生 SSISDB 故障转移时停机时间近乎零，请将两个 Azure SSIS IR 保持运行状态。 只有主要 Azure-SSIS IR 可以访问主要 SSISDB 来提取和执行包，还可以写入包执行日志，而辅助 Azure-SSIS IR 只能对部署在其他位置的包（例如在 Azure 文件存储中）执行相同操作。

   如果要最大程度地降低运行成本，可以在创建辅助 Azure-SSIS IR 后将其停止。 发生 SSISDB 故障转移时，主要和辅助 Azure-SSIS IR 将交换角色。 如果主要 Azure-SSIS IR 停止，则需要重新启动它。 根据它是否注入到虚拟网络和使用的注入方法，运行时间在 5 分钟或 20 - 30 分钟左右。

1. 如果[使用用于业务流程/计划包执行的 ADF](./how-to-invoke-ssis-package-ssis-activity.md)，请确保所有带有执行 SSIS 包活动和关联触发器的相关 ADF 管道都复制到辅助 ADF（触发器最初处于禁用状态）。 发生 SSISDB 故障转移时，需要启用它们。

1. 你可以[测试 Azure SQL 数据库故障转移组](../azure-sql/database/failover-group-add-single-database-tutorial.md?tabs=azure-portal#3---test-failover)，并在 [ADF 门户中查看 Azure-SSIS IR 监视页](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal)，无论主要和辅助 Azure SSIS IR 是否已交换角色。 

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-managed-instance-failover-group"></a>使用 Azure SQL 托管实例故障转移组配置双重备用 Azure-SSIS IR 对

若要配置与 Azure SQL 托管实例故障转移组同步运行的双重备用 Azure-SSIS IR 对，请完成以下步骤。

1. 使用 Azure 门户，可以在主要 Azure SQL 托管实例的“故障转移组”页上[为主要和辅助 Azure SQL 托管实例创建故障转移组](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal)。

1. 使用 Azure 门户/ADF UI，你可以创建一个带有主要 Azure SQL 托管实例的新 Azure-SSIS IR，以在主要区域中托管 SSISDB。 如果现有 Azure-SSIS IR 已附加到 Azure SQL 托管实例托管的 SSIDB，并且仍在运行，则需要先将其停止才能重新配置它。 这将是主要 Azure-SSIS IR。

   在“集成运行时设置”窗格的“部署设置”页上[选择使用 SSISDB](./create-azure-ssis-integration-runtime.md#creating-ssisdb)时，请选中“结合使用双重备用 Azure-SSIS Integration Runtime 对和 SSISDB 故障转移”复选框。 对于“双重备用对名称”，请输入一个名称以标识主要和辅助 Azure-SSIS IR 对。 完成主要 Azure-SSIS IR 的创建后，它将启动并附加到主要 SSISDB，该 SSISDB 将以你的身份创建，具有读写访问权限。 如果刚重新配置了它，则需要重新启动。 还可以检查是否已在辅助 Azure SQL 托管实例的“概述”页上将主要 SSISDB 复制到具有只读访问权限的辅助 SSISDB。

1. 使用 Azure 门户/ADF UI，你可以通过辅助 Azure SQL 托管实例创建另一个 Azure-SSIS IR 来托管次要区域中的 SSISDB。 这将是你的辅助 Azure-SSIS IR。 对于完整的 BCDR，请确保还会在次要区域中创建它所依赖的所有资源，例如，用于存储自定义安装程序脚本/文件的 Azure 存储、用于业务流程/计划包执行的 ADF 等。

   在“集成运行时设置”窗格的“部署设置”页上[选择使用 SSISDB](./create-azure-ssis-integration-runtime.md#creating-ssisdb)时，请选中“结合使用双重备用 Azure-SSIS Integration Runtime 对和 SSISDB 故障转移”复选框。 对于“双重备用对名称”，请输入相同名称以标识主要和辅助 Azure-SSIS IR 对。 完成辅助 Azure-SSIS IR 的创建后，它将启动并附加到辅助 SSISDB。

1. Azure SQL 托管实例可以通过使用数据库主密钥 (DMK) 加密数据库中的敏感数据（例如 SSISDB）来保护这些数据。 默认情况下，DMK 本身使用服务主密钥 (SMK) 进行加密。 撰写本文时，Azure SQL 托管实例故障转移组不会从主 Azure SQL 托管实例复制 SMK，因此，在发生故障转移后，DMK 和 SSISDB 无法在辅助 Azure SQL 托管实例上解密。 若要解决这个问题，可以添加一个密码加密，以在辅助 Azure SQL 托管实例上对 DMK 进行解密。 使用 SSMS 完成以下步骤。

   1. 为主要 Azure SQL 托管实例中的 SSISDB 运行以下命令，以添加用于加密 DMK 的密码。

      ```sql
      ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'YourPassword'
      ```
   
   1. 在主要和辅助 Azure SQL 托管实例中，为 SSISDB 运行以下命令，以添加用于解密 DMK 的新密码。

      ```sql
      EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'YourPassword', @action = N'add'
      ```

1. 如果希望在发生 SSISDB 故障转移时停机时间近乎零，请将两个 Azure SSIS IR 保持运行状态。 只有主要 Azure-SSIS IR 可以访问主要 SSISDB 来提取和执行包，还可以写入包执行日志，而辅助 Azure-SSIS IR 只能对部署在其他位置的包（例如在 Azure 文件存储中）执行相同操作。

   如果要最大程度地降低运行成本，可以在创建辅助 Azure-SSIS IR 后将其停止。 发生 SSISDB 故障转移时，主要和辅助 Azure-SSIS IR 将交换角色。 如果主要 Azure-SSIS IR 停止，则需要重新启动它。 根据它是否注入到虚拟网络和使用的注入方法，运行时间在 5 分钟或 20 - 30 分钟左右。

1. 如果[使用 Azure SQL 托管实例代理进行业务流程/计划包执行](./how-to-invoke-ssis-package-managed-instance-agent.md)，请确保将所有相关的 SSIS 作业及其作业步骤和关联的计划都复制到辅助 Azure SQL 托管实例（这些计划最初处于禁用状态）。 使用 SSMS 完成以下步骤。

   1. 对于每个 SSIS 作业，右键单击并选择“将作业脚本撰写为”、“创建到”以及“新建查询编辑器窗口”下拉菜单项来生成脚本。

      ![生成 SSIS 作业脚本](media/configure-bcdr-azure-ssis-integration-runtime/generate-ssis-job-script.png)

   1. 对于每个生成的 SSIS 作业脚本，查找用于执行 `sp_add_job` 存储过程的命令，并根据需要修改/删除 `@owner_login_name` 参数的赋值。

   1. 对于每个更新的 SSIS 作业脚本，请在辅助 Azure SQL 托管实例上运行该脚本，以复制该作业及其作业步骤和关联的计划。

   1. 使用以下脚本创建新的 T-SQL 作业，以在主要和辅助 Azure SQL 托管实例中基于主要/辅助 SSISDB 角色分别启用/禁用 SSIS 作业计划，并定期运行。 发生 SSISDB 故障转移时，将启用禁用的 SSIS 作业计划，反之亦然。

      ```sql
      IF (SELECT Top 1 role_desc FROM SSISDB.sys.dm_geo_replication_link_status WHERE partner_database = 'SSISDB') = 'PRIMARY'
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 0
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 1
         END
      ELSE
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 1
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 0
         END
      ```

1. 如果[使用用于业务流程/计划包执行的 ADF](./how-to-invoke-ssis-package-ssis-activity.md)，请确保所有带有执行 SSIS 包活动和关联触发器的相关 ADF 管道都复制到辅助 ADF（触发器最初处于禁用状态）。 发生 SSISDB 故障转移时，需要启用它们。

1. 你可以[测试 Azure SQL 托管实例故障转移组](../azure-sql/managed-instance/failover-group-add-instance-tutorial.md?tabs=azure-portal#test-failover)，并在 [ADF 门户中查看 Azure-SSIS IR 监视页](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal)，无论主要和辅助 Azure SSIS IR 是否已交换角色。 

## <a name="attach-a-new-azure-ssis-ir-to-existing-ssisdb-hosted-by-azure-sql-databasemanaged-instance"></a>将新 Azure-SSIS IR 附加到 Azure SQL 数据库/托管实例所托管的现有 SSISDB 中

如果发生灾难影响现有 Azure-SSIS IR，但不影响同一区域中的 Azure SQL 数据库/托管实例，则可以将其替换为另一个区域中的新 Azure-SSIS IR。 若要将 Azure SQL 数据库/托管实例托管的现有 SSISDB 附加到新 Azure-SSIS IR，请完成以下步骤。

1. 如果现有 Azure-SSIS IR 仍在运行，则需要先使用 Azure 门户/ADF UI 或 Azure PowerShell 停止它。 如果灾难还影响同一区域中的 ADF，则可以跳过此步骤。

1. 使用 SSMS，对 Azure SQL 数据库/托管实例中的 SSISDB 运行以下命令，以更新将允许来自新 ADF/Azure-SSIS IR 的连接的元数据。

   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name = 'YourNewADF', @integration_runtime_name = 'YourNewAzureSSISIR'
   ```

1. 使用 [Azure 门户/ADF UI](./create-azure-ssis-integration-runtime.md#use-the-azure-portal-to-create-an-integration-runtime) 或 [Azure PowerShell](./create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)，在另一个区域中分别创建名为 YourNewADF/YourNewAzureSSISIR 的新 ADF/Azure-SSIS IR。 如果使用 Azure 门户/ADF UI，则可以忽略“集成运行时设置”窗格的“部署设置”页上的测试连接错误。

## <a name="next-steps"></a>后续步骤

可以考虑 Azure-SSIS IR 的以下其他配置选项：

- [为 Azure-SSIS IR 配置包存储](./create-azure-ssis-integration-runtime.md#creating-azure-ssis-ir-package-stores)

- [为 Azure-SSIS IR 配置自定义设置](./how-to-configure-azure-ssis-ir-custom-setup.md)

- [为 Azure-SSIS IR 配置虚拟网络注入](./join-azure-ssis-integration-runtime-virtual-network.md)

- [将自承载 IR 配置为 Azure-SSIS IR 的代理](./self-hosted-integration-runtime-proxy-ssis.md)