---
title: 用于 Azure SQL 数据库中的数据库的虚拟网络终结点和规则
description: 将子网标记为虚拟网络服务终结点。 然后，将该终结点作为虚拟网络规则添加到数据库的 ACL。 然后，数据库就会接受来自子网上所有虚拟机和其他节点的通信。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: d480239c0eb99ed48c13ec2fdb5b052574acc318
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092493"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>使用适用于 Azure SQL 数据库中的服务器的虚拟网络服务终结点和规则

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*虚拟网络规则* 是一种防火墙安全功能，用于控制 [azure SQL 数据库](sql-database-paas-overview.md) 中的数据库和弹性池的服务器，或者 [是否接受从虚拟网络中的](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 特定子网发送的通信。 本文介绍为何虚拟网络规则有时是安全地允许在 SQL 数据库和 Azure Synapse 分析中与数据库进行通信的最佳选项。

> [!NOTE]
> 本文适用于 SQL 数据库和 Azure Synapse 分析。 为简单起见，术语 " *数据库* " 指的是 SQL 数据库和 Azure Synapse Analytics 中的两个数据库。 同样，对 *服务器* 的任何引用都指托管 sql 数据库和 Azure Synapse Analytics 的 [逻辑 SQL 服务器](logical-servers.md) 。

若要创建虚拟网络规则，首先必须具有可供规则引用的[虚拟网络服务终结点][vm-virtual-network-service-endpoints-overview-649d]。

## <a name="create-a-virtual-network-rule"></a>创建虚拟网络规则

如果只想要创建虚拟网络规则，则可以跳到 [本文后面](#anchor-how-to-by-using-firewall-portal-59j)的步骤和说明。

## <a name="details-about-virtual-network-rules"></a>虚拟网络规则详细信息

此部分介绍虚拟网络规则的多项详细信息。

### <a name="only-one-geographic-region"></a>只有一个地理区域

每个虚拟网络服务终结点仅适用于一个 Azure 区域。 终结点不允许其他区域接受来自子网的通信。

任何虚拟网络规则都只能应用于基础终结点应用到的区域。

### <a name="server-level-not-database-level"></a>服务器级别，而不是数据库级别

每个虚拟网络规则都适用于整个服务器，而不仅仅是该服务器上某个特定的数据库。 换句话说，虚拟网络规则在服务器级别应用，而不是在数据库级别应用。

与之不同的是，IP 规则适用于这其中的任一级别。

### <a name="security-administration-roles"></a>安全管理角色

虚拟网络服务终结点的管理中的安全角色分离。 下述每个角色都需要进行操作：

- **网络管理员 ([网络参与者](../../role-based-access-control/built-in-roles.md#network-contributor) 角色) ：** &nbsp;启用终结点。
- **数据库管理员 ([SQL Server 参与者](../../role-based-access-control/built-in-roles.md#sql-server-contributor) 角色) ：** &nbsp;更新 ACL)  (的访问控制列表，将给定子网添加到服务器。

#### <a name="azure-rbac-alternative"></a>Azure RBAC 替代项

网络管理员和数据库管理员角色的权限超出虚拟网络规则的管理需要， 只有部分权限是必需的。

可以选择在 Azure 中使用[基于角色的访问控制 (RBAC)][rbac-what-is-813s]，创建一个只有部分必需权限的自定义角色。 可以使用自定义角色，而不是涉及网络管理员或数据库管理员。如果你将用户添加到自定义角色，并将用户添加到其他两个主要管理员角色，则你的安全公开的外围应用会较低。

> [!NOTE]
> 在某些情况下，SQL 数据库和虚拟网络子网中的数据库位于不同的订阅中。 在这些情况下，必须确保以下配置：
>
> - 这两个订阅必须处于相同的 Azure Active Directory (Azure AD) 租户。
> - 用户具有启动操作所需的权限，例如，启用服务终结点并将虚拟网络子网添加到给定的服务器。
> - 两个订阅都必须注册 Microsoft.Sql 提供程序。

## <a name="limitations"></a>限制

对于 SQL 数据库，虚拟网络规则功能具有以下限制：

- 在 SQL 数据库中的数据库的防火墙中，每个虚拟网络规则都引用一个子网。 引用的所有这些子网都必须托管在同一个托管数据库的地理区域内。
- 每个服务器最多可以有128个虚拟网络的 ACL 条目。
- 虚拟网络规则仅适用于 Azure 资源管理器虚拟网络，不适用于 [经典部署模型][arm-deployment-model-568f] 网络。
- 启用虚拟网络服务终结点到 SQL 数据库还能为 Azure Database for MySQL 和 Azure Database for PostgreSQL 启用终结点。 将终结点设置为 **ON 时**，尝试从终结点连接到 Azure Database for MySQL 或 Azure Database for PostgreSQL 实例可能会失败。
  - 根本原因在于 Azure Database for MySQL 和 Azure Database for PostgreSQL 可能没有配置虚拟网络规则。 您必须为 Azure Database for MySQL 和 Azure Database for PostgreSQL 配置虚拟网络规则，并且连接将成功。
  - 若要在已配置专用终结点的 SQL 逻辑服务器上定义虚拟网络防火墙规则，请将 " **拒绝公共网络访问权限** " 设置为 " **否**"。
- 在防火墙上，IP 地址范围适用于以下网络项，但虚拟网络规则不会：
  - [站点到站点 (S2S) 虚拟专用网络 (VPN) ][vpn-gateway-indexmd-608y]
  - 通过[Azure ExpressRoute](../../expressroute/index.yml)本地

### <a name="considerations-when-you-use-service-endpoints"></a>使用服务终结点时的注意事项

使用 SQL 数据库的服务终结点时，请查看以下注意事项：

- **需要出站到 Azure SQL 数据库公共 Ip。** 必须为 SQL 数据库 Ip 打开 (Nsg) 的网络安全组以允许连接。 为此，可以使用 SQL 数据库的 NSG [服务标记](../../virtual-network/network-security-groups-overview.md#service-tags) 。

### <a name="expressroute"></a>ExpressRoute

如果你在本地使用 [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ，则对于公共对等互连或 Microsoft 对等互连，你将需要识别使用的 NAT IP 地址。 进行公共对等互连时，每条 ExpressRoute 线路默认情况下会使用两个 NAT IP 地址。当流量进入 Microsoft Azure 网络主干时，会向 Azure 服务流量应用这些地址。 对于 Microsoft 对等互连，使用的 NAT IP 地址由客户或服务提供商提供。 若要允许访问服务资源，必须在资源 IP 防火墙设置中允许这些公共 IP 地址。 若要查找公共对等互连 ExpressRoute 线路 IP 地址，请通过 Azure 门户[开具 ExpressRoute 支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 若要详细了解适用于 ExpressRoute 公共和 Microsoft 对等互连的 NAT，请参阅 [Azure 公共对等互连的 nat 要求](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)。

若要允许从线路到 SQL 数据库的通信，必须为 NAT 的公共 IP 地址创建 IP 网络规则。

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>将虚拟网络服务终结点用于 Azure 存储的影响

Azure 存储已实现相同的功能，允许限制到 Azure 存储帐户的连接。 如果选择将此功能与 SQL 数据库使用的 Azure 存储帐户一起使用，则可能会遇到问题。 下一步是对受此影响的 SQL 数据库和 Azure Synapse Analytics 功能的列表和讨论。

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Azure Synapse Analytics PolyBase 和 COPY 语句

PolyBase 和 COPY 语句通常用于从 Azure 存储帐户将数据加载到 Azure Synapse 分析，以实现高吞吐量数据引入。 如果要将数据加载到其中的 Azure 存储帐户限制仅访问一组虚拟网络子网，则使用 PolyBase 并将 COPY 语句复制到存储帐户时，连接将中断。 若要通过将 COPY 和 PolyBase 用于连接到受保护到虚拟网络的 Azure 存储的 Azure Synapse Analytics 启用导入和导出方案，请按照本部分中的步骤进行操作。

#### <a name="prerequisites"></a>先决条件

- 使用 [本指南](/powershell/azure/install-az-ps)安装 Azure PowerShell。
- 如果有通用 v1 或 Azure Blob 存储帐户，则必须先按照 [升级到常规用途 v2 存储帐户](../../storage/common/storage-account-upgrade.md)中的步骤升级到常规用途 v2。
- 对于 "Azure 存储帐户 **防火墙和虚拟网络** 设置" 菜单下的 "**允许受信任的 Microsoft 服务访问此存储帐户**"，你必须启用此功能。 启用此配置将允许 PolyBase 和 COPY 语句使用强身份验证连接到存储帐户，在此情况下，网络流量将保留在 Azure 主干上。 有关详细信息，请参阅 [此指南](../../storage/common/storage-network-security.md#exceptions)。

> [!IMPORTANT]
> SQL 数据库仍支持 PowerShell Azure 资源管理器模块，但所有将来的开发都适用于 Az .Sql 模块。 AzureRM 模块至少在 2020 年 12 月之前将继续接收 bug 修补程序。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 若要详细了解其兼容性，请参阅[新 Azure PowerShell Az 模块简介](/powershell/azure/new-azureps-module-az)。

#### <a name="steps"></a>步骤

1. 如果有独立的专用 SQL 池，请使用 PowerShell 将 SQL server 注册到 Azure AD：

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Azure Synapse Analytics 工作区中的专用 SQL 池不需要执行此步骤。

1. 如果有 Azure Synapse Analytics 工作区，请注册工作区的系统管理的标识：

   1. 在 Azure 门户中转到 Azure Synapse Analytics 工作区。
   2. 中转到 " **托管标识** " 窗格。
   3. 请确保已启用 " **允许管道** " 选项。
   
1. 按照 [创建存储帐户](../../storage/common/storage-account-create.md)中的步骤创建一个 **常规用途 v2 存储帐户**。

   > [!NOTE]
   >
   > - 如果有通用 v1 或 Blob 存储帐户，则必须先按照 [升级到常规用途 v2 存储帐户](../../storage/common/storage-account-upgrade.md)中的步骤 *升级到 v2* 。
   > - 有关 Azure Data Lake Storage Gen2 的已知问题，请参阅 [Azure Data Lake Storage Gen2 的已知问题](../../storage/blobs/data-lake-storage-known-issues.md)。

1. 在存储帐户下，中转到 " **访问控制 (IAM)**，然后选择" **添加角色分配**"。 将 **存储 Blob 数据参与者** Azure 角色分配给托管专用 SQL 池的服务器或工作区，该服务器或已注册到 Azure AD 的工作区。

   > [!NOTE]
   > 只有拥有存储帐户权限的成员才可以执行此步骤。 有关各种 Azure 内置角色，请参阅 [azure 内置角色](../../role-based-access-control/built-in-roles.md)。
  
1. 若要启用到 Azure 存储帐户的 PolyBase 连接，请执行以下操作：

   1. 如果之前未创建数据库 [主密钥](/sql/t-sql/statements/create-master-key-transact-sql) ，请创建一个。

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. 创建 **标识为 "托管服务标识"** 的数据库范围的凭据。

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - 无需使用 Azure 存储访问密钥指定机密，因为此机制使用 [托管标识](../../active-directory/managed-identities-azure-resources/overview.md) 。
       > - 用于 PolyBase 连接的标识名称应为 **"托管服务标识"** ，才能使用安全连接到虚拟网络的 Azure 存储帐户。

   1. 使用 PolyBase 创建一个外部数据源， `abfss://` 用于连接到常规用途 v2 存储帐户。

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - 如果已有与常规用途 v1 或 Blob 存储帐户关联的外部表，则应首先删除这些外部表。 然后删除相应的外部数据源。 接下来，使用 `abfss://` 连接到常规用途 v2 存储帐户的方案创建外部数据源，如上所示。 然后，使用新的外部数据源重新创建所有外部表。 您可以使用 " [生成和发布脚本向导](/sql/ssms/scripting/generate-and-publish-scripts-wizard) " 为所有外部表生成创建脚本，以方便使用。
       > - 有关方案的详细信息 `abfss://` ，请参阅 [使用 Azure Data Lake Storage Gen2 URI](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md)。
       > - 有关创建外部数据源的详细信息，请参阅 [此指南](/sql/t-sql/statements/create-external-data-source-transact-sql)。

   1. 使用 [外部表](/sql/t-sql/statements/create-external-table-transact-sql)作为一般查询。

### <a name="sql-database-blob-auditing"></a>SQL 数据库 blob 审核

Blob 审核将审核日志推送到你自己的存储帐户。 如果此存储帐户使用虚拟网络服务终结点功能，则从 SQL 数据库到存储帐户的连接将中断。

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>向服务器中添加虚拟网络防火墙规则

很久以前，在此功能得到增强之前，你需要先启用虚拟网络服务终结点，然后才能在防火墙中实现实时虚拟网络规则。 与 SQL 数据库中的数据库相关的终结点。 从2018年1月起，可以通过设置 **IgnoreMissingVNetServiceEndpoint** 标志来规避这一要求。 现在，你可以向服务器添加虚拟网络防火墙规则，而无需启用虚拟网络服务终结点。

仅设置防火墙规则并不能帮助保护服务器的安全。 你还必须启用虚拟网络服务终结点，以使安全生效。 当你启用服务终结点时，你的虚拟网络子网会经历停机，直到它完成从关闭到的转换。 这一停机时间在大型虚拟网络的上下文中尤其如此。 可以使用 **IgnoreMissingVNetServiceEndpoint** 标志，减少或消除转换期间的停机时间。

可以使用 PowerShell 设置 **IgnoreMissingVNetServiceEndpoint** 标志。 有关详细信息，请参阅 [PowerShell 为 SQL 数据库创建虚拟网络服务终结点和规则][sql-db-vnet-service-endpoint-rule-powershell-md-52d]。

## <a name="errors-40914-and-40615"></a>错误 40914 和 40615

连接错误40914与 Azure 门户的 "**防火墙**" 窗格中指定的 *虚拟网络规则* 相关。 错误40615类似，只不过它与防火墙上的 *IP 地址规则* 相关。

### <a name="error-40914"></a>错误 40914

**消息正文：** "无法打开该登录请求的服务器"*[服务器名称]*"。 不允许客户端访问服务器。 "

错误说明：客户端位于包含虚拟网络服务器终结点的子网中。 不过，服务器没有授权子网与数据库进行通信的虚拟网络规则。

**错误解决方法：** 在 Azure 门户的 " **防火墙** " 窗格上，使用 "虚拟网络规则" 控件为子网 [添加虚拟网络规则](#anchor-how-to-by-using-firewall-portal-59j) 。

### <a name="error-40615"></a>错误 40615

**消息正文：** "无法打开 {0} 该登录请求的服务器" "。 不允许 IP 地址为 "" 的客户端 {1} 访问服务器。

**错误说明：** 客户端正在尝试从无权连接到服务器的 IP 地址进行连接。 服务器防火墙没有 IP 地址规则允许客户端从给定 IP 地址与数据库进行通信。

错误解决方法：输入客户端 IP 地址作为 IP 规则。 使用 Azure 门户中的 " **防火墙** " 窗格执行此步骤。

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>使用门户创建虚拟网络规则

本部分说明如何使用 [Azure 门户][http-azure-portal-link-ref-477t] 在 SQL 数据库中的数据库中创建 *虚拟网络规则* 。 此规则可告知数据库接受来自已标记为 *虚拟网络服务终结点* 的特定子网的通信。

> [!NOTE]
> 如果要将服务终结点添加到服务器的虚拟网络防火墙规则，请首先确保为子网启用服务终结点。
>
> 如果没有为子网启用服务终结点，门户会要求你启用它们。 在添加规则的相同窗格上选择 " **启用** " 按钮。

## <a name="powershell-alternative"></a>PowerShell 备用

脚本还可以使用 PowerShell cmdlet **AzSqlServerVirtualNetworkRule** 或 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create)创建虚拟网络规则。 如果感兴趣，请参阅 [PowerShell，为 SQL 数据库创建虚拟网络服务终结点和规则][sql-db-vnet-service-endpoint-rule-powershell-md-52d]。

## <a name="rest-api-alternative"></a>REST API 替代项

在内部，用于 SQL 虚拟网络操作的 PowerShell cmdlet 调用 REST Api。 可以直接调用 REST API。

- [虚拟网络规则：操作][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>先决条件

你必须已有一个使用与 SQL 数据库相关的特定虚拟网络服务终结点 *类型名称* 标记的子网。

- 相关的终结点类型名称为 Microsoft.Sql。
- 如果子网可能未使用类型名称进行标记，请参阅[验证子网是否是终结点][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]。

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure 门户步骤

1. 登录到 [Azure 门户][http-azure-portal-link-ref-477t]。

1. 搜索并选择 " **SQL 服务器**"，然后选择你的服务器。 在“安全性”下，选择“防火墙和虚拟网络” 。

1. 将 " **允许访问 Azure 服务** " 设置为 " **关闭**"。

    > [!IMPORTANT]
    > 如果将该控件设置为 **ON**，则服务器将接受来自 Azure 边界内的任何子网的通信。 这是来自被识别为 Azure 数据中心定义的范围内的某个 IP 地址的通信。 将控件设置为 **"开** " 可能对安全点的访问权限很高。 与 SQL 数据库的虚拟网络规则功能结合在一起的 Microsoft Azure 虚拟网络服务终结点功能可以减少安全面。

1. 在 "**虚拟网络**" 部分中选择 " **+ 添加现有**"。

    ![屏幕截图，显示选择 + 添加现有 (子网终结点作为 SQL 规则) 。][image-portal-firewall-vnet-add-existing-10-png]

1. 在 "新建 **创建/更新** " 窗格中，在框中填写 Azure 资源的名称。

    > [!TIP]
    > 必须为子网包含正确的地址前缀。 可以在门户中找到 **地址前缀** 值。 中转到 **所有** &gt; **类型** 的 &gt; **虚拟网络**。 筛选器会显示虚拟网络。 选择虚拟网络，然后选择 " **子网**"。 " **地址范围** " 列包含所需的地址前缀。

    ![显示新规则的框的屏幕截图。][image-portal-firewall-create-update-vnet-rule-20-png]

1. 选择窗格底部附近的 **"确定"** 按钮。

1. 请在 " **防火墙** " 窗格中查看生成的虚拟网络规则。

    ![屏幕截图，显示 "防火墙" 窗格上的新规则。][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> 以下状态适用于这些规则：
>
> - **Ready**：指示启动的操作已成功。
> - **Failed**：指示你所启动的操作已失败。
> - **已删除**：仅适用于删除操作，指示规则已被删除且不再适用。
> - **InProgress**：指示操作正在进行。 操作处于这种状态时，会应用旧规则。

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>相关文章

- [Azure 虚拟网络服务终结点][vm-virtual-network-service-endpoints-overview-649d]
- [服务器级和数据库级防火墙规则][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>后续步骤

- [使用 PowerShell 为 SQL 数据库创建虚拟网络服务终结点，然后创建虚拟网络规则][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [虚拟网络规则：][rest-api-virtual-network-rules-operations-862r] 包含 REST Api 的操作

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
