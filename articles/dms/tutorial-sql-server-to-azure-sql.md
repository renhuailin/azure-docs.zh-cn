---
title: 教程：将 SQL Server 脱机迁移到 Azure SQL 数据库
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure 数据库迁移服务从 SQL Server 脱机迁移到 Azure SQL 数据库。
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/03/2021
ms.openlocfilehash: 5afe39d7693d36ebfd433c6cd1db336e9c72b7ca
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664231"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-using-dms"></a>教程：使用 DMS 将 SQL Server 迁移到 Azure SQL 数据库

可以使用 Azure 数据库迁移服务将数据库从 SQL Server 实例迁移到 [Azure SQL 数据库](/azure/sql-database/)。 在本教程中，将通过使用 Azure 数据库迁移服务，将还原到 SQL Server 2016（或更高版本）本地实例的 [AdventureWorks2016](/sql/samples/adventureworks-install-configure#download-backup-files) 数据库迁移到 Azure SQL 数据库中的单一数据库或共用数据库。

将了解如何执行以下操作：
> [!div class="checklist"]
>
> - 使用数据迁移助手评估你的本地数据库是否存在任何阻碍性问题。
> - 使用数据迁移助手迁移数据库示例架构。 
> - 注册 Azure DataMigration 资源提供程序。
> - 创建 Azure 数据库迁移服务的实例。
> - 使用 Azure 数据库迁移服务创建迁移项目。
> - 运行迁移。
> - 监视迁移。


## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

- 下载并安装 [SQL Server 2016 或更高版本](https://www.microsoft.com/sql-server/sql-server-downloads)。
- 按照[启用或禁用服务器网络协议](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文中的说明启用 TCP/IP 协议（在安装 SQL Server Express 时，会默认禁用它）。
- [将 AdventureWorks2016 数据库恢复到 SQL Server 实例。](/sql/samples/adventureworks-install-configure#restore-to-sql-server)
- 遵循[使用 Azure 门户在 Azure SQL 数据库中创建数据库](../azure-sql/database/single-database-create-quickstart.md)一文中的详细信息，在 Azure SQL 数据库中创建数据库。 出于本教程的目的，假设 Azure SQL 数据库的名称是“AdventureWorksAzure”，但是你可按照自己意愿使用任意名称命名。

    > [!NOTE]
    > 如果你使用 SQL Server Integration Services (SSIS) 并且希望将 SSIS 项目/包的目录数据库 (SSISDB) 从 SQL Server 迁移到 Azure SQL 数据库，则当你在 Azure 数据工厂 (ADF) 中预配 SSIS 时，系统会代表你自动创建和管理目标 SSISDB。 有关如何迁移 SSIS 包的详细信息，请参阅[将 SQL Server Integration Services 包迁移到 Azure](./how-to-migrate-ssis-packages.md)。
  
- 下载并安装最新版本的[数据迁移助手](https://www.microsoft.com/download/details.aspx?id=53595)。
- 使用 Azure 资源管理器部署模型创建适合 Azure 数据库迁移服务的 Microsoft Azure 虚拟网络，它将使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 为本地源服务器提供站点到站点连接。 有关创建虚拟网络的详细信息，请参阅[虚拟网络文档](../virtual-network/index.yml)，尤其是提供了分步详细信息的快速入门文章。

    > [!NOTE]
    > 在虚拟网络设置期间，如果将 ExpressRoute 与 Microsoft 的网络对等互连一起使用，则请将以下服务[终结点](../virtual-network/virtual-network-service-endpoints-overview.md)添加到要在其中预配该服务的子网：
    >
    > - 目标数据库终结点（例如，SQL 终结点、Cosmos DB 终结点等）
    > - 存储终结点
    > - 服务总线终结点
    >
    > Azure 数据库迁移服务缺少 Internet 连接，因此必须提供此配置。
    >
    >如果在本地网络与 Azure 之间没有站点到站点连接，或者站点到站点连接带宽有限，请考虑在混合模式下使用 Azure 数据库迁移服务（预览版）。 混合模式利用本地迁移工作线程以及云中运行的 Azure 数据库迁移服务的实例。 若要在混合模式下创建 Azure 数据库迁移服务的实例，请参阅[使用 Azure 门户在混合模式下创建 Azure 数据库迁移服务的实例](./quickstart-create-data-migration-service-hybrid-portal.md)一文。

- 请确保虚拟网络的网络安全组出站安全规则不会阻止 ServiceBus、存储和 AzureMonitor 的 ServiceTag 出站端口 443。 有关 Azure 虚拟网络 NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/virtual-network-vnet-plan-design-arm.md)一文。
- 配置[针对数据库引擎访问的 Windows 防火墙](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
- 打开 Windows 防火墙，使 Azure 数据库迁移服务能够访问源 SQL Server（默认情况下为 TCP 端口 1433）。 如果默认实例正在侦听其他端口，请将该端口添加到防火墙。
- 如果使用动态端口运行多个命名 SQL Server 实例，则可能需要启用 SQL Browser 服务并允许通过防火墙访问 UDP 端口 1434，以便 Azure 数据库迁移服务可连接到源服务器上的命名实例。
- 在源数据库的前面使用了防火墙设备时，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库。
- 为 Azure SQL 数据库创建服务器级 IP [防火墙规则](../azure-sql/database/firewall-configure.md)，以允许 Azure 数据库迁移服务访问目标数据库。 提供用于 Azure 数据库迁移服务的虚拟网络子网范围。
- 确保用于连接到源 SQL Server 实例的凭据具有 [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql) 权限。
- 确保用于连接到目标数据库实例的凭据具有目标 Azure SQL 数据库的 [CONTROL DATABASE](/sql/t-sql/statements/grant-database-permissions-transact-sql) 权限。

    > [!IMPORTANT]
    > 创建 Azure 数据库迁移服务实例需要访问通常不在同一资源组中的虚拟网络设置。 因此，创建 DMS 实例的用户需要订阅级别的权限。 若要创建所需的角色（你可以根据需要分配这些角色），请运行以下脚本：
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="assess-your-on-premises-database"></a>访问本地数据库

在将数据从 SQL Server 实例迁移到 Azure SQL 数据库中的单一数据库或共用数据库之前，需要对 SQL Server 数据库进行评估，了解任何可能会阻止迁移的阻碍性问题。 使用数据迁移助手，按照[执行 SQL Server 迁移评估](/sql/dma/dma-assesssqlonprem)一文中所述的步骤，完成对本地数据库的评估。 所需步骤汇总如下：

1. 在数据迁移助手中，选择“新建 (+)”图标，然后选择“评估”项目类型。
2. 指定项目名称。 在“评估类型”下拉列表中，选择“数据库引擎”，在“源服务器类型”文本框中，选择“SQL Server”，在“目标服务器类型”中，选择“Azure SQL 数据库”，然后选择“创建”，从而创建项目      。

    评估迁移到 Azure SQL 数据库中的单一数据库或共用数据库的源 SQL Server 数据库时，可以选择以下一种或两种评估报告类型：

   - 检查数据库兼容性
   - 检查功能奇偶校验

    默认情况下会选择这两种报告类型。

3. 在数据迁移助手的“选项”屏幕上，选择“下一步”。
4. 在“选择源”屏幕上的“连接到服务器”对话框中，向 SQL Server 提供连接详细信息，然后选择“连接”。
5. 在“添加源”对话框中，依次选择“AdventureWorks2016”、“添加”和“开始评估”   。

    > [!NOTE]
    > 如果你使用 SSIS，则 DMA 目前不支持对源 SSISDB 进行评估。 但是，将 SSIS 项目/包重新部署到由 Azure SQL 数据库托管的目标 SSISDB 时，会对其进行评估/验证。 有关如何迁移 SSIS 包的详细信息，请参阅[将 SQL Server Integration Services 包迁移到 Azure](./how-to-migrate-ssis-packages.md)。

    评估完成后，结果将如下图所示：

    ![评估数据迁移](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    对于 Azure SQL 数据库中的数据库，评估结果可标识出在部署到单一数据库或共用数据库时会出现的功能奇偶一致性问题和迁移阻塞性问题。

    - SQL Server 功能奇偶校验类别在 Azure 中提供了一套全面的建议、可用的替代方法和缓解步骤，以帮助你在迁移项目中规划工作。
    - “兼容性问题”类别说明了部分支持的或完全不支持的功能，这些功能反映了可能会阻止 SQL Server 数据库迁移到 Azure SQL 数据库的兼容性问题。 此外，还提供了一些建议来帮助你解决这些问题。

6. 通过选择特定的选项来查看迁移阻塞问题和功能奇偶校验问题的评估结果。

## <a name="migrate-the-sample-schema"></a>迁移示例架构

如果对评估感到满意，并确信所选数据库适合迁移到 Azure SQL 数据库中的单一数据库或共用数据库，请使用 DMA 将架构迁移到 Azure SQL 数据库。

> [!NOTE]
> 在数据迁移助手中创建迁移项目之前，请确保已按照先决条件中的说明在 Azure 中预配了数据库。 

> [!IMPORTANT]
> 如果你使用 SSIS，则 DMA 目前不支持迁移源 SSISDB，但你可以将 SSIS 项目/包重新部署到由 Azure SQL 数据库托管的目标 SSISDB。 有关如何迁移 SSIS 包的详细信息，请参阅[将 SQL Server Integration Services 包迁移到 Azure](./how-to-migrate-ssis-packages.md)。

要将 AdventureWorks2016 架构迁移到 Azure SQL 数据库中的单一数据库或共用数据库，请执行以下步骤：

1. 在数据迁移助手中，选择“新建 (+)”图标，然后在“项目类型”下选择“迁移” 。
2. 指定项目名称，在“源服务器类型”文本框中，选择“SQL Server”，然后在“目标服务器类型”文本框中，选择“Azure SQL 数据库”。
3. 在“迁移范围”下，选择“仅架构”。

    在执行前面的步骤后，数据迁移助手界面应如下图所示：

    ![创建数据迁移助手项目](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. 选择“创建”来创建项目。
5. 在数据迁移助手中，指定 SQL Server 的源连接详细信息，依次选择“连接”和“AdventureWorks2016”数据库。

    ![数据迁移助手源连接详细信息](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. 在“连接到目标服务器”下选择“下一步”，指定 Azure SQL 数据库的目标连接详细信息，选择“连接”，然后选择已在 Azure SQL 数据库中预配的“AdventureWorksAzure”数据库。

    ![数据迁移助手目标连接详细信息](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. 选择“下一步”，以转到“选择对象”屏幕，可以在其中指定需要部署到 Azure SQL 数据库的“AdventureWorks2016”中的架构对象  。

    默认情况下，选择所有对象。

    ![生成 SQL 脚本](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. 选择“生成 SQL 脚本”以创建 SQL 脚本，然后检查脚本是否有任何错误。

    ![架构脚本](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. 选择“部署架构”以将架构部署到 Azure SQL 数据库，然后在部署架构后，检查目标服务器是否存在任何异常情况。

    ![部署架构](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]   

## <a name="create-an-azure-database-migration-service-instance"></a>创建 Azure 数据库迁移服务实例

1. 在 Azure 门户菜单或“主页”页上，选择“创建资源” 。 搜索并选择“Azure 数据库迁移服务”。

    ![Azure 市场](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. 在“Azure 数据库迁移服务”屏幕上，选择“创建”   。

    ![创建 Azure 数据库迁移服务实例](media/tutorial-sql-server-to-azure-sql/dms-create-1.png)
  
3. 在“创建迁移服务”基本信息屏幕中：

     - 选择订阅。
     - 新建资源组或使用现有资源组。
     - 指定 Azure 数据库迁移服务实例的名称。
     - 选择要在其中创建 Azure 数据库迁移服务实例的位置。
     - 选择“Azure”作为服务模式。
     - 选择定价层。 有关成本和定价层的详细信息，请参阅[价格页](https://aka.ms/dms-pricing)。

    ![配置 Azure 数据库迁移服务实例基本信息设置](media/tutorial-sql-server-to-azure-sql/dms-settings-2.png)

     - 选择“下一页:网络”。

4. 在“创建迁移服务”网络屏幕中：

    - 选择现有虚拟网络或新建一个。 虚拟网络为 Azure 数据库迁移服务提供了对源 SQL Server 和目标 Azure SQL 数据库实例的访问权限。 有关如何在 Azure 门户中创建虚拟网络的详细信息，请参阅[使用 Azure 门户创建虚拟网络](../virtual-network/quick-create-portal.md)一文。

    ![配置 Azure 数据库迁移服务实例网络设置](media/tutorial-sql-server-to-azure-sql/dms-settings-3.png)

    - 选择“查看 + 创建”，查看详细信息，然后选择“创建”以创建服务 。

## <a name="create-a-migration-project"></a>创建迁移项目

创建服务后，在 Azure 门户中找到并打开它，然后创建一个新的迁移项目。

1. 在 Azure 门户菜单中，选择“所有服务”。 搜索并选择“Azure 数据库迁移服务”。

     ![查找 Azure 数据库迁移服务的所有实例](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. 在“Azure 数据库迁移服务”屏幕上，搜索你创建的 Azure 数据库迁移服务实例。

3. 选择“新建迁移项目”。

     ![查找 Azure 数据库迁移服务实例](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. 在“新建迁移项目”屏幕上指定项目名称，在“源服务器类型”文本框中选择“SQL Server”，在“目标服务器类型”文本框中选择“Azure SQL 数据库”，然后针对“选择迁移活动类型”选择“数据迁移”。

    ![创建数据库迁移服务项目](media/tutorial-sql-server-to-azure-sql/dms-create-project-2.png)

5. 选择“创建并运行活动”，以便创建项目并运行迁移活动。 

## <a name="specify-source-details"></a>指定源详细信息

1. 在“选择源”屏幕上，指定源 SQL Server 实例的连接详细信息。

    请确保为源 SQL Server 实例名称使用完全限定的域名 (FQDN)。 即使不能使用 DNS 名称解析，也可使用 IP 地址。

2. 如果尚未在源服务器上安装受信任的证书，请选中“信任服务器证书”复选框。

    如果没有安装受信任的证书，SQL Server 会在实例启动时生成自签名证书。 此证书用于加密客户端连接的凭据。

    > [!CAUTION]
    > 使用自签名证书加密的 TLS 连接不提供强安全性。 它们易遭受中间人攻击。 在生产环境中或在连接到 Internet 的服务器上不应依赖使用自签名证书的 TLS。

    > [!IMPORTANT]
    > 如果你使用 SSIS，则 DMS 目前不支持迁移源 SSISDB，但你可以将 SSIS 项目/包重新部署到由 Azure SQL 数据库托管的目标 SSISDB。 有关如何迁移 SSIS 包的详细信息，请参阅[将 SQL Server Integration Services 包迁移到 Azure](./how-to-migrate-ssis-packages.md)。

   ![源详细信息](media/tutorial-sql-server-to-azure-sql/dms-source-details-2.png)
   
3. 选择“下一步: 选择数据库”。

## <a name="select-databases-for-migration"></a>选择要迁移的数据库

选择要迁移到 Azure SQL 数据库的所有数据库或特定数据库。 DMS 会为你提供所选数据库的预期迁移时间。 如果迁移停机时间是可接受的，请继续迁移。 如果迁移停机时间是不可接受的，请考虑迁移到 [SQL 托管实例（停机时间几乎为零）](tutorial-sql-server-managed-instance-online.md)，或者联系 [DMS 团队](mailto:DMSFeedback@microsoft.com)以了解其他选择。 

1. 从可用数据库列表中选择要迁移的数据库。 
1. 查看预期的停机时间。 如果该时间是可以接受的，则选择“下一步: 选择目标 >>”

   ![源数据库](media/tutorial-sql-server-to-azure-sql/select-database.png)



## <a name="specify-target-details"></a>指定目标详细信息

1. 在“选择目标”屏幕上，为 Azure SQL 数据库提供身份验证设置。 

   ![选择目标](media/tutorial-sql-server-to-azure-sql/select-target.png)
   
   > [!NOTE]
   > 目前，SQL 身份验证是唯一受支持的身份验证类型。

1. 在完成时选择“下一步:映射到目标数据库”屏幕，映射源和目标数据库以进行迁移。

    如果目标数据库包含的数据库名称与源数据库的相同，则 Azure 数据库迁移服务默认会选择目标数据库。

    ![映射到目标数据库](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity-2.png)

1. 在完成时选择“下一步:配置迁移设置”，展开表列表，然后查看受影响字段的列表。

    Azure 数据库迁移服务会自动选择目标 Azure SQL 数据库实例上的所有空源表。 若要重新迁移已含数据的表，需要在此边栏选项卡上明确选择表。

    ![选择表](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity-2.png)

1. 在完成时选择“下一步:摘要”屏幕，查看迁移配置，然后在“活动名称”文本框中指定迁移活动的名称。

    ![选择“验证”选项](media/tutorial-sql-server-to-azure-sql/dms-configuration-2.png)

## <a name="run-the-migration"></a>运行迁移

- 选择“开始迁移”。

    迁移活动窗口随即出现，活动的“状态”为“挂起” 。

    ![活动状态](media/tutorial-sql-server-to-azure-sql/dms-activity-status-1.png)

## <a name="monitor-the-migration"></a>监视迁移

1. 在迁移活动屏幕上，选择“刷新”  来更新显示，直到迁移的“状态”  显示为“已完成”  。

    ![活动状态已完成](media/tutorial-sql-server-to-azure-sql/dms-completed-activity-1.png)

2. 验证目标 Azure SQL 数据库上的目标数据库。

## <a name="additional-resources"></a>其他资源

- 若要了解 Azure 数据库迁移服务，请参阅[什么是 Azure 数据库迁移服务？](./dms-overview.md)一文。
- 若要了解 Azure SQL 数据库，请参阅[什么是 Azure SQL 数据库服务？](../azure-sql/database/sql-database-paas-overview.md)一文。
