---
title: 教程：将 SQL Server 联机迁移到 SQL 托管实例
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure 数据库迁移服务执行从 SQL Server 到 Azure SQL 托管实例的联机迁移。
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 08/20/2021
ms.openlocfilehash: 4c9028b559c537c0707b0ab3264fbfa11d26a350
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664089"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-online-using-dms"></a>教程：使用 DMS 将 SQL Server 联机迁移到 Azure SQL 托管实例

可使用 Azure 数据库迁移服务以最少停机时间将数据库从 SQL Server 实例迁移到 [Azure SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)。 有关需要一些手动操作的其他方法，请参阅[将 SQL Server 实例迁移到 Azure SQL 托管实例](../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)一文。

本教程介绍如何使用 Azure 数据库迁移服务，以最少停机时间将 [AdventureWorks2016](/sql/samples/adventureworks-install-configure#download-backup-files) 数据库从 SQL Server 的本地实例迁移到 SQL 托管实例。

将了解如何执行以下操作：
> [!div class="checklist"]
>
> * 注册 Azure DataMigration 资源提供程序。
> * 创建 Azure 数据库迁移服务的实例。
> * 使用 Azure 数据库迁移服务创建迁移项目并开始联机迁移。
> * 监视迁移。
> * 准备就绪后执行迁移交接。

> [!IMPORTANT]
> 若要使用 Azure 数据库迁移服务从 SQL Server 联机迁移到 SQL 托管实例，必须在 SMB 网络共享中提供完整的数据库备份和后续日志备份，供服务用来迁移数据库。 Azure 数据库迁移服务不启动任何备份，而是使用现有备份进行迁移。你可能已经在灾难恢复计划中有了这些备份。
> 确保[使用 WITH CHECKSUM 选项进行备份](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?preserve-view=true&view=sql-server-2017)。 可以将每个备份写入单个独立的备份文件或多个备份文件。 但是，不支持将多个备份（即完整备份和 t-log 备份）追加到单个备份介质中。 最后，可以使用压缩的备份来减少遇到与迁移大型备份相关的潜在问题的可能性。

> [!NOTE]
> 使用 Azure 数据库迁移服务执行联机迁移需要基于“高级”定价层创建实例。

> [!IMPORTANT]
> 为获得最佳迁移体验，Microsoft 建议在目标数据库所在的 Azure 区域中创建 Azure 数据库迁移服务的实例。 跨区域或地理位置移动数据可能会减慢迁移过程并引入错误。

> [!IMPORTANT]
> 尽量缩短联机迁移过程的持续时间，将实例重新配置或计划维护造成的中断风险降到最低。 如果发生此类事件，迁移过程将从头开始。 如果是计划内维护，则在重新启动迁移过程之前，会有 36 小时的宽限期。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文介绍如何从 SQL Server 联机迁移到 SQL 托管实例。 有关脱机迁移，请参阅[使用 DMS 将 SQL Server 脱机迁移到 SQL 托管实例](tutorial-sql-server-to-managed-instance.md)。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* 下载并安装 [SQL Server 2016 或更高版本](https://www.microsoft.com/sql-server/sql-server-downloads)。
* 按照[启用或禁用服务器网络协议](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文中的说明启用 TCP/IP 协议（在安装 SQL Server Express 时，会默认禁用它）。
* [将 AdventureWorks2016 数据库还原到SQL Server实例。](/sql/samples/adventureworks-install-configure#restore-to-sql-server)
* 使用 Azure 资源管理器部署模型创建适合 Azure 数据库迁移服务的 Microsoft Azure 虚拟网络，它将使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 为本地源服务器提供站点到站点连接。 [了解使用 Azure 数据库迁移服务迁移 SQL 托管实例的网络拓扑](./resource-network-topologies.md)。 有关创建虚拟网络的详细信息，请参阅[虚拟网络文档](../virtual-network/index.yml)，尤其是提供了分步详细信息的快速入门文章。

    > [!NOTE]
    > 在虚拟网络设置期间，如果将 ExpressRoute 与 Microsoft 的网络对等互连一起使用，则请将以下服务[终结点](../virtual-network/virtual-network-service-endpoints-overview.md)添加到要在其中预配该服务的子网：
    >
    > * 目标数据库终结点（例如，SQL 终结点、Cosmos DB 终结点等）
    > * 存储终结点
    > * 服务总线终结点
    >
    > Azure 数据库迁移服务缺少 Internet 连接，因此必须提供此配置。
    >
    >如果在本地网络与 Azure 之间没有站点到站点连接，或者站点到站点连接带宽有限，请考虑在混合模式下使用 Azure 数据库迁移服务（预览版）。 混合模式利用本地迁移工作线程以及云中运行的 Azure 数据库迁移服务的实例。 若要在混合模式下创建 Azure 数据库迁移服务的实例，请参阅[使用 Azure 门户在混合模式下创建 Azure 数据库迁移服务的实例](./quickstart-create-data-migration-service-portal.md)一文。

    > [!IMPORTANT]
    > 关于在迁移过程中使用的存储帐户，必须执行以下操作之一：
    > * 选择允许所有网络访问该存储器帐户。
    > * 在 MI 子网中启用[子网委托](../virtual-network/manage-subnet-delegation.md)，并更新存储帐户防火墙规则以允许此子网。

* 请确保虚拟网络的网络安全组规则不阻止 ServiceBus、存储服务和 AzureMonitor 的 ServiceTag 出站端口 443。 有关虚拟网络 NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/virtual-network-vnet-plan-design-arm.md)一文。
* 配置[针对源数据库引擎访问的 Windows 防火墙](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* 打开 Windows 防火墙，使 Azure 数据库迁移服务能够访问源 SQL Server（默认情况下为 TCP 端口 1433）。 如果默认实例正在侦听其他端口，请将该端口添加到防火墙。
* 如果使用动态端口运行多个命名 SQL Server 实例，则可能需要启用 SQL Browser 服务并允许通过防火墙访问 UDP 端口 1434，以便 Azure 数据库迁移服务可连接到源服务器上的命名实例。
* 如果在源数据库的前面使用了防火墙设备，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库，并通过 SMB 端口 445 访问文件。
* 按照[在 Azure 门户中创建 SQL 托管实例](../azure-sql/managed-instance/instance-create-quickstart.md)一文中的详述创建 SQL 托管实例。
* 确保用于连接源 SQL Server 和目标 SQL 托管实例的登录名是 sysadmin 服务器角色的成员。
* 提供一个 SMB 网络共享，其中包含可由 Azure 数据库迁移服务用来执行数据库迁移的所有完整数据库备份文件和后续事务日志备份文件。
* 确保运行源 SQL Server 实例的服务帐户对你创建的网络共享拥有写入权限，并且源服务器的计算机帐户具有对同一共享的读/写访问权限。
* 请记下在前面创建的网络共享中拥有完全控制权限的 Windows 用户（和密码）。 Azure 数据库迁移服务可模拟用户凭据，将备份文件上传到 Azure 存储容器，以执行还原操作。
* 创建一个 Azure Active Directory 应用程序 ID，用于生成可由 Azure 数据库迁移服务用来连接目标 Azure 数据库托管实例和 Azure 存储容器的应用程序 ID 密钥。 有关详细信息，请参阅[使用门户创建可访问资源的 Azure Active Directory 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md)一文。

  > [!NOTE]
  > Azure 数据库迁移服务使用的应用程序 ID 支持服务主体的机密（基于密码的）身份验证。 它不支持基于证书的身份验证。

  > [!NOTE]
  > Azure 数据库迁移服务需要对指定的应用程序 ID 的订阅具有参与者权限。 或者，你可以创建自定义角色，以授予 Azure 数据库迁移服务所需的特定权限。 有关使用自定义角色的分步指导，请参阅[用于 SQL Server 到 SQL 托管实例联机迁移的自定义角色](./resource-custom-roles-sql-db-managed-instance.md)一文。

* 创建或记下可让 DMS 服务将数据库备份文件上传到的并可用来迁移数据库的 **标准性能层** Azure 存储帐户。  请务必在创建 Azure 数据库迁移服务实例的同一区域创建 Azure 存储帐户。

  > [!NOTE]
  > 使用联机迁移选项将[透明数据加密](../azure-sql/database/transparent-data-encryption-tde-overview.md)保护的数据库迁移到托管实例时，必须先迁移本地或 Azure VM SQL Server 实例中的相应证书，再还原数据库。 有关详细步骤，请参阅[将 TDE 证书迁移到托管实例](../azure-sql/database/transparent-data-encryption-tde-overview.md)。

[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]

## <a name="create-an-azure-database-migration-service-instance"></a>创建 Azure 数据库迁移服务实例

1. 在 Azure 门户菜单或“主页”页上，选择“创建资源” 。 搜索并选择“Azure 数据库迁移服务”。

    ![Azure 市场](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. 在“Azure 数据库迁移服务”屏幕上，选择“创建”   。

    ![创建 Azure 数据库迁移服务实例](media/tutorial-sql-server-to-managed-instance-online/dms-create-service-1.png)

3. 在“创建迁移服务”基本信息屏幕中：

     - 选择订阅。
     - 新建资源组或使用现有资源组。
     - 指定 Azure 数据库迁移服务实例的名称。
     - 选择要在其中创建 Azure 数据库迁移服务实例的位置。
     - 选择“Azure”作为服务模式。
     - 从“高级”定价层选择 SKU。 
     
      > [!NOTE]
      > 仅当使用“高级”层时，才支持联机迁移。

     - 有关成本和定价层的详细信息，请参阅[价格页](https://aka.ms/dms-pricing)。

    ![配置 Azure 数据库迁移服务实例基本信息设置](media/tutorial-sql-server-to-managed-instance-online/dms-create-service-2.png)

     - 选择“下一页:网络”。

4. 在“创建迁移服务”网络屏幕中：

    - 选择现有虚拟网络或新建一个。 虚拟网络为 Azure 数据库迁移服务提供源 SQL Server 和目标 Azure SQL 托管实例的访问权限。
     
    - 有关如何在 Azure 门户中创建虚拟网络的详细信息，请参阅[使用 Azure 门户创建虚拟网络](../virtual-network/quick-create-portal.md)一文。
    
    - 有关更多详细信息，请参阅[使用 Azure 数据库迁移服务迁移 Azure SQL 托管实例的网络拓扑](./resource-network-topologies.md)一文。

      ![配置 Azure 数据库迁移服务实例网络设置](media/tutorial-sql-server-to-managed-instance-online/dms-create-service-3.png)

    - 选择“查看 + 创建”，查看详细信息，然后选择“创建”以创建服务 。

## <a name="create-a-migration-project"></a>创建迁移项目

创建服务实例后，在 Azure 门户中找到并打开它，然后创建一个新的迁移项目。

1. 在 Azure 门户菜单中，选择“所有服务”。 搜索并选择“Azure 数据库迁移服务”。

    ![查找 Azure 数据库迁移服务的所有实例](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. 在“Azure 数据库迁移服务”屏幕上，搜索你创建的 Azure 数据库迁移服务实例。

3. 选择“新建迁移项目”。

     ![查找 Azure 数据库迁移服务实例](media/tutorial-sql-server-to-managed-instance-online/dms-create-project-1.png)

4. 在“新建迁移项目”屏幕上指定项目名称，在“源服务器类型”文本框中选择“SQL Server”，在“目标服务器类型”文本框中选择“Azure SQL 数据库托管实例”，然后在“选择活动类型”中选择“联机数据迁移”。       

   ![创建数据库迁移服务项目](media/tutorial-sql-server-to-managed-instance-online/dms-create-project-2.png)

5. 选择“创建并运行活动”，以便创建项目并运行迁移活动。 

## <a name="specify-source-details"></a>指定源详细信息

1. 在“选择源”屏幕上，指定源 SQL Server 实例的连接详细信息。

    请确保为源 SQL Server 实例名称使用完全限定的域名 (FQDN)。 即使不能使用 DNS 名称解析，也可使用 IP 地址。

2. 如果尚未在服务器上安装受信任的证书，请选中“信任服务器证书”复选框。

    如果没有安装受信任的证书，SQL Server 会在实例启动时生成自签名证书。 此证书用于加密客户端连接的凭据。

    > [!CAUTION]
    > 使用自签名证书加密的 TLS 连接不提供强安全性。 它们易遭受中间人攻击。 在生产环境中或在连接到 Internet 的服务器上不应依赖使用自签名证书的 TLS。

    ![源详细信息](media/tutorial-sql-server-to-managed-instance-online/dms-source-details.png)

3. 选择“下一步: 选择目标”

## <a name="specify-target-details"></a>指定目标详细信息

1. 在“选择目标”屏幕上，指定可由 DMS 实例用来连接 SQL 托管实例目标实例和 Azure 存储帐户的应用程序 ID 和密钥  。

    有关详细信息，请参阅[使用门户创建可访问资源的 Azure Active Directory 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md)一文。

2. 选择包含 SQL 托管实例目标实例的订阅，然后选择目标 SQL 托管实例。

    如果尚未预配 SQL 托管实例，请选择相应的[链接](../azure-sql/managed-instance/instance-create-quickstart.md)来帮助预配实例。 SQL 托管实例准备就绪后，请返回到此特定项目以执行迁移。

3. 提供用于连接到 SQL 托管实例的“SQL 用户”和“密码”。

    ![选择目标](media/tutorial-sql-server-to-managed-instance-online/dms-target-details.png)

4. 选择“下一步: 选择数据库”。

## <a name="specify-source-databases"></a>指定源数据库

1. 在“选择数据库”屏幕上，选择要迁移的源数据库。

  ![选择源数据库](media/tutorial-sql-server-to-managed-instance-online/dms-source-database.png)

  > [!IMPORTANT]
  > 如果使用 SQL Server Integration Services (SSIS)，则 DMS 目前不支持将 SSIS 项目/包的目录数据库 (SSISDB) 从 SQL Server 迁移到 SQL 托管实例。 但是，你可以在 Azure 数据工厂 (ADF) 中预配 SSIS 并将 SSIS 项目/包重新部署到由 SQL 托管实例托管的目标 SSISDB。 有关如何迁移 SSIS 包的详细信息，请参阅[将 SQL Server Integration Services 包迁移到 Azure](./how-to-migrate-ssis-packages.md)。

2. 选择“下一步: 配置迁移设置”。

## <a name="configure-migration-settings"></a>配置迁移设置

1. 在“配置迁移设置”屏幕上，提供以下详细信息：

    | 参数 | 说明 |
    |--------|---------|
    |**SMB 网络位置共享** | 本地 SMB 网络共享或 Azure 文件共享，其中包含可由 Azure 数据库迁移服务用来执行迁移的完整数据库备份文件和事务日志备份文件。 运行源 SQL Server 实例的服务帐户必须对此网络共享拥有读/写特权。 在网络共享中提供服务器的 FQDN 或 IP 地址，例如“'\\\servername.domainname.com\backupfolder”或“\\\IP address\backupfolder”。 为提高性能，建议为每个要迁移的数据库使用单独的文件夹。 可以使用“高级设置”选项提供数据库级文件共享路径。 如果在连接到 SMB 共享时遇到问题，请查看 [SMB 共享](known-issues-azure-sql-db-managed-instance-online.md#smb-file-share-connectivity)。 |
    |**用户名** | 确保 Windows 用户具有对上面提供的网络共享的完全控制权限。 Azure 数据库迁移服务将模拟用户凭据，将备份文件上传到 Azure 存储容器，以执行还原操作。 如果使用 Azure 文件共享，请将带 AZURE\ 前缀的存储帐户名称用作用户名。 |
    |**密码** | 用户密码。 如果使用 Azure 文件共享，请将存储帐户密钥用作密码。 |
    |**Azure 存储帐户的订阅** | 选择包含 Azure 存储帐户的订阅。 |
    |**Azure 存储帐户** | 选择可让 DMS 将备份文件从 SMB 网络共享上传到的并用于数据库迁移的 Azure 存储帐户。  为获得最佳文件上传性能，我们建议在 DMS 服务所在的同一区域中选择存储帐户。 |

    ![配置迁移设置](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings.png)

    > [!NOTE]
    > 如果 Azure 数据库迁移服务显示“系统错误 53”或“系统错误 57”错误，原因可能是 Azure 数据库迁移服务无法访问 Azure 文件共享。 如果遇到这其中的一个错误，请按[此处](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)的说明操作，授予从虚拟网络访问存储帐户的权限。

    > [!IMPORTANT]
    > 如果启用了环回检查功能，并且源 SQL Server 和文件共享位于同一台计算机上，则源将无法使用 FQDN 访问文件共享。 若要解决此问题，请按照[此处](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd)的说明禁用环回检查功能。

2. 在完成时选择“下一步:摘要”。

## <a name="review-the-migration-summary"></a>查看迁移摘要

1. 在“摘要”屏幕的“活动名称”文本框中，指定迁移活动的名称 。

2. 查看并验证与迁移项目关联的详细信息。

    ![迁移项目摘要](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary.png)

## <a name="run-and-monitor-the-migration"></a>运行并监视迁移

1. 选择“开始迁移”。

2. “迁移活动”窗口随即显示，其中显示了当前数据库迁移状态。 选择“刷新”可更新显示。

   ![正在进行的迁移活动](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration.png)

    可以进一步展开数据库和登录类别，以监视相应服务器对象的迁移状态。

   ![迁移活动状态](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend.png)

## <a name="performing-migration-cutover"></a>执行迁移交接

在 SQL 托管实例的目标实例上还原整个数据库备份之后，可以使用该数据库执行迁移交接。

1. 如果已准备好完成联机数据库迁移，请选择“开始交接”。

2. 停止所有传入源数据库的流量。

3. 执行 [结尾日志备份]，使备份文件在 SMB 网络共享中可用，然后等到还原这最后一个事务日志备份。

    此时，会看到“挂起的更改”设置为 0。

4. 依次选择“确认”、“应用” 。

    ![准备完成交接](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

    > [!IMPORTANT]
    > 交接后，具有业务关键服务层的 SQL 托管实例只能用比具有常规用途服务层的 SQL 托管实例更长的时间才能使用，因为必须为 AlwaysOn 高可用性组设定三个辅助副本的种子。 此操作持续时间取决于数据的大小，有关详细信息，请参阅[管理操作持续时间](../azure-sql/managed-instance/management-operations-overview.md#duration)。

5. 当数据库迁移状态显示为“已完成”后，请将应用程序连接到 SQL 托管实例的新目标实例。

    ![交接完成](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="additional-resources"></a>其他资源

* 有关介绍如何使用 T-SQL RESTORE 命令将数据库迁移到 SQL 托管实例的教程，请参阅[使用 restore 命令将备份还原到 SQL 托管实例](../azure-sql/managed-instance/restore-sample-database-quickstart.md)。
* 有关 SQL 托管实例的信息，请参阅[什么是 SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)。
* 若要了解如何将应用连接到 SQL 托管实例，请参阅[连接应用程序](../azure-sql/managed-instance/connect-application-instance.md)。
