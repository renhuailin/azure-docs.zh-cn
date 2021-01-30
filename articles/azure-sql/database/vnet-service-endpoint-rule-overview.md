---
title: Azure SQL 数据库中数据库的虚拟网络终结点和规则
description: 将子网标记为虚拟网络服务终结点。 然后，将该终结点作为虚拟网络规则添加到你的数据库的 ACL。 然后，数据库就会接受来自子网上所有虚拟机和其他节点的通信。
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
ms.openlocfilehash: 0dcffe6731c177d1d45c569361fcb200f23af86c
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095352"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>使用适用于 Azure SQL 数据库中的服务器的虚拟网络服务终结点和规则

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*虚拟网络规则* 是一种防火墙安全功能，用于控制 [azure sql 数据库](sql-database-paas-overview.md) 中的数据库和弹性池的服务器、 (以前在 [AZURE SYNAPSE 分析](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 中的 sql DW) 数据库是否接受从虚拟网络中的特定子网发送的通信。 本文说明了为何有时候最好选择虚拟网络规则来安全地启用与 Azure SQL 数据库和 Azure Synapse Analytics 中数据库的通信。

> [!NOTE]
> 本文同时适用于 Azure SQL 数据库和 Azure Synapse Analytics。 为简单起见，术语“数据库”是指 Azure SQL 数据库中的数据库和 Azure Synapse Analytic 中的数据库。 同样，无论何时提及“服务器”，都是指承载着 Azure SQL 数据库和 Azure Synapse Analytics 的[逻辑 SQL 服务器](logical-servers.md)。

若要创建虚拟网络规则，首先必须具有可供规则引用的[虚拟网络服务终结点][vm-virtual-network-service-endpoints-overview-649d]。

## <a name="create-a-virtual-network-rule"></a>创建虚拟网络规则

如果只想创建虚拟网络规则，则可跳到[本文后面](#anchor-how-to-by-using-firewall-portal-59j)的步骤和说明。

## <a name="details-about-virtual-network-rules"></a>虚拟网络规则详细信息

此部分介绍虚拟网络规则的多项详细信息。

### <a name="only-one-geographic-region"></a>只有一个地理区域

一个虚拟网络服务终结点只适用于一个 Azure 区域。 终结点不允许其他区域接受来自子网的通信。

任何虚拟网络规则都只能应用于基础终结点应用到的区域。

### <a name="server-level-not-database-level"></a>服务器级而非数据库级

每个虚拟网络规则都适用于整个服务器，而不仅仅是该服务器上某个特定的数据库。 换句话说，虚拟网络规则在服务器级而非数据库级应用。

与之不同的是，IP 规则适用于这其中的任一级别。

### <a name="security-administration-roles"></a>安全管理角色

在管理虚拟网络服务终结点时，安全角色是分开的。 下述每个角色都需要进行操作：

- 网络管理员（[网络参与者](../../role-based-access-control/built-in-roles.md#network-contributor)）角色：启用终结点。
- 数据库管理员（[SQL Server 参与者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)）角色：更新访问控制列表 (ACL)，将给定的子网添加到服务器。

#### <a name="azure-rbac-alternative"></a>Azure RBAC 替代项

网络管理员和数据库管理员角色的权限超出虚拟网络规则的管理需要， 只有部分权限是必需的。

可以选择在 Azure 中使用[基于角色的访问控制 (RBAC)][rbac-what-is-813s]，创建一个只有部分必需权限的自定义角色。 在涉及到网络管理员或数据库管理员时，可以使用自定义角色来代替。与向两个主要的管理员角色添加用户相比，向自定义角色添加用户的安全风险较低。

> [!NOTE]
> 在某些情况下，Azure SQL 数据库中的数据库和虚拟网络子网位于不同的订阅中。 在这些情况下，必须确保以下配置：
>
> - 两个订阅都必须属于同一 Azure Active Directory (Azure AD) 租户。
> - 用户具有启动多项操作（例如启用服务终结点和向给定服务器添加虚拟网络子网）所需的权限。
> - 两个订阅都必须注册 Microsoft.Sql 提供程序。

## <a name="limitations"></a>限制

对于 Azure SQL 数据库，虚拟网络规则功能具有以下限制：

- 在 Azure SQL 数据库中数据库的防火墙中，每个虚拟网络规则都引用一个子网。 引用的所有这些子网都必须托管在同一个托管数据库的地理区域内。
- 对于任何虚拟网络，每个服务器最多可以有 128 个 ACL 条目。
- 虚拟网络规则仅适用于 Azure 资源管理器虚拟网络，不适用于[经典部署模型][arm-deployment-model-568f]网络。
- 如果启用 Azure SQL 数据库的虚拟网络服务终结点，则会同时启用 Azure Database for MySQL 和 Azure Database for PostgreSQL 的终结点。 当终结点设置为“启用”时，尝试从终结点连接到 Azure Database for MySQL 或 Azure Database for PostgreSQL 实例可能会失败。
  - 根本原因是 Azure Database for MySQL 和 Azure Database for PostgreSQL 可能没有配置虚拟网络规则。 必须为 Azure Database for MySQL 和 Azure Database for PostgreSQL 配置虚拟网络规则，连接才会成功。
  - 若要在已配置专用终结点的 SQL 逻辑服务器上定义虚拟网络防火墙规则，请将 " **拒绝公共网络访问权限** " 设置为 " **否**"。
- 在防火墙上，IP 地址范围适用于以下网络项，但虚拟网络规则并不适用：
  - [站点到站点 (S2S) 虚拟专用网络 (VPN)][vpn-gateway-indexmd-608y]
  - 通过 [Azure ExpressRoute](../../expressroute/index.yml) 建立的本地网络

### <a name="considerations-when-you-use-service-endpoints"></a>使用服务终结点时的注意事项

在使用 Azure SQL 数据库的服务终结点时，请查看以下注意事项：

- 需要到 Azure SQL 数据库公共 IP 的出站连接。 必须为 Azure SQL 数据库 IP 启用网络安全组 (NSG) 才能进行连接。 为此，可以将 NSG [服务标记](../../virtual-network/network-security-groups-overview.md#service-tags)用于 Azure SQL 数据库。

### <a name="expressroute"></a>ExpressRoute

如果是在本地使用 [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，则在进行公共对等互连或 Microsoft 对等互连时，需标识所用的 NAT IP 地址。 进行公共对等互连时，每条 ExpressRoute 线路默认情况下会使用两个 NAT IP 地址。当流量进入 Microsoft Azure 网络主干时，会向 Azure 服务流量应用这些地址。 在进行 Microsoft 对等互连时，所用 NAT IP 地址由客户或服务提供商提供。 若要允许访问服务资源，必须在资源 IP 防火墙设置中允许这些公共 IP 地址。 若要查找公共对等互连 ExpressRoute 线路 IP 地址，请通过 Azure 门户[开具 ExpressRoute 支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 若要详细了解适用于 ExpressRoute 公共对等互连和 Microsoft 对等互连的 NAT，请参阅 [Azure 公共对等互连的 NAT 要求](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)。

若要允许从线路到 Azure SQL 数据库的通信，必须为 NAT 的公共 IP 地址创建 IP 网络规则。

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>将虚拟网络服务终结点与 Azure 存储配合使用的影响

Azure 存储已实现相同的功能，允许限制到 Azure 存储帐户的连接。 如果选择将此功能与某个 Azure 存储帐户配合使用，而该帐户正由 Azure SQL 数据库使用，则可能会出现问题。 接下来会列出受此影响的 Azure SQL 数据库和 Azure Synapse Analytics 功能并对其进行讨论。

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Azure Synapse Analytics PolyBase 和 COPY 语句

PolyBase 和 COPY 语句通常用于将数据从 Azure 存储帐户加载到 Azure Synapse Analytics 中，以实现高吞吐量数据引入。 如果你要从中加载数据的 Azure 存储帐户将访问权限限定于一组虚拟网络子网，则使用 PolyBase 和 COPY 语句时，与存储帐户的连接会断开。 对于与 Azure 存储（已通过安全方式连接到虚拟网络）连接的 Azure Synapse Analytics，若要通过 COPY 和 PolyBase 来启用导入和导出方案，请执行本部分的步骤。

#### <a name="prerequisites"></a>先决条件

- 按照[此指南](/powershell/azure/install-az-ps)安装 Azure PowerShell。
- 如果你有常规用途 v1 或 Azure Blob 存储帐户，则必须先按照[升级到常规用途 v2 存储帐户](../../storage/common/storage-account-upgrade.md)中的步骤升级到常规用途 v2。
- 必须在 Azure 存储帐户的“防火墙和虚拟网络”设置菜单下 启用“允许受信任的 Microsoft 服务访问此存储帐户”。 启用此配置将允许 PolyBase 和 COPY 语句使用强身份验证连接到存储帐户，而网络流量仍保留在 Azure 主干上。 有关详细信息，请参阅[此指南](../../storage/common/storage-network-security.md#exceptions)。

> [!IMPORTANT]
> Azure SQL 数据库仍然支持 PowerShell Azure 资源管理器模块，但所有后续开发都针对 Az.Sql 模块。 AzureRM 模块至少在 2020 年 12 月之前将继续接收 bug 修补程序。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 若要详细了解其兼容性，请参阅[新 Azure PowerShell Az 模块简介](/powershell/azure/new-azureps-module-az)。

#### <a name="steps"></a>步骤

1. 如果你有独立的专用 SQL 池，请使用 PowerShell 向 Azure AD 注册 SQL Server：

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
   
1. 遵循[创建存储帐户](../../storage/common/storage-account-create.md)中的步骤创建一个常规用途 v2 存储帐户。

   > [!NOTE]
   >
   > - 如果你有常规用途 v1 或 Blob 存储帐户，则必须按照[升级到常规用途 v2 存储帐户](../../storage/common/storage-account-upgrade.md)中的步骤先升级到 v2。
   > - 有关 Azure Data Lake Storage Gen2 的已知问题，请参阅 [Azure Data Lake Storage Gen2 的已知问题](../../storage/blobs/data-lake-storage-known-issues.md)。

1. 在你的存储帐户下，转到“访问控制(IAM)”，然后选择“添加角色分配”。  将 **存储 Blob 数据参与者** Azure 角色分配给托管专用 SQL 池的服务器或工作区，该服务器或已注册到 Azure AD 的工作区。

   > [!NOTE]
   > 只有对存储帐户具有“所有者”权限的成员才能执行此步骤。 有关各种 Azure 内置角色，请参阅 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。
  
1. 若要启用到 Azure 存储帐户的 PolyBase 连接，请执行以下操作：

   1. 创建数据库[主密钥](/sql/t-sql/statements/create-master-key-transact-sql)（如果此前尚未创建）。

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. 使用 IDENTITY = '托管服务标识' 创建以数据库为作用域的凭据。

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - 使用 Azure 存储访问密钥时，不需指定 SECRET，因为此机制在后台使用[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。
       > - 使用 Azure 存储帐户以安全方式连接到虚拟网络时，IDENTITY 名称应该为 '托管服务标识'，才能通过 PolyBase 进行连接。

   1. 使用 `abfss://` 方案创建一个外部数据源，以便通过 PolyBase 连接到常规用途 v2 存储帐户。

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - 如果已经有外部表关联到常规用途 v1 或 Blob 存储帐户，则应先删除这些外部表。 然后删除相应的外部数据源。 接下来，使用 `abfss://` 方案创建一个外部数据源，以便连接到常规用途 v2 存储帐户，如前所示。 然后，使用这个新的外部数据源重新创建所有外部表。 可以通过[生成和发布脚本向导](/sql/ssms/scripting/generate-and-publish-scripts-wizard)为所有外部表生成 create-script，以方便使用。
       > - 有关 `abfss://` 方案的详细信息，请参阅[使用 Azure Data Lake Storage Gen2 URI](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md)。
       > - 有关 CREATE EXTERNAL DATA SOURCE 的详细信息，请参阅[此指南](/sql/t-sql/statements/create-external-data-source-transact-sql)。

   1. 使用[外部表](/sql/t-sql/statements/create-external-table-transact-sql)进行正常查询。

### <a name="sql-database-blob-auditing"></a>Azure SQL 数据库 Blob 审核

Blob 审核将审核日志推送到你自己的存储帐户。 如果此存储帐户使用虚拟网络服务终结点功能，则会断开从 Azure SQL 数据库到存储帐户的连接。

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>向服务器添加虚拟网络防火墙规则

很久以前，此功能尚未得到增强，系统会要求你先启用虚拟网络服务终结点，然后才能在防火墙中实施实时虚拟网络规则。 这些终结点已将给定的虚拟网络子网关联到 Azure SQL 数据库中的数据库。 从 2018 年 1 月起，可以通过设置 IgnoreMissingVNetServiceEndpoint 标志来规避此要求。 现在，你可以向服务器添加虚拟网络防火墙规则，而无需启用虚拟网络服务终结点。

仅设置防火墙规则无助于保护服务器。 你还必须启用虚拟网络服务终结点才能使安全措施生效。 启用服务终结点时，你的虚拟网络子网会经历停机，直到完成从关闭到开启的转换。 在使用大型虚拟网络的情况下，这一停机时间尤其明显。 可以使用 **IgnoreMissingVNetServiceEndpoint** 标志，减少或消除转换期间的停机时间。

可以使用 PowerShell 设置 **IgnoreMissingVNetServiceEndpoint** 标志。 有关详细信息，请参阅[使用 PowerShell 创建 Azure SQL 数据库的虚拟网络服务终结点和规则][sql-db-vnet-service-endpoint-rule-powershell-md-52d]。

## <a name="errors-40914-and-40615"></a>错误 40914 和 40615

连接错误 40914 与虚拟网络规则（如 Azure 门户中的“防火墙”窗格所指定的那样）相关。 错误 40615 基本相似，不同之处在于它与防火墙上的 IP 地址规则相关。

### <a name="error-40914"></a>错误 40914

消息文本：“无法打开登录时请求的服务器 ‘[server-name]’。 不允许客户端访问服务器。”

错误说明：客户端位于包含虚拟网络服务器终结点的子网中。 不过，服务器没有授权子网与数据库进行通信的虚拟网络规则。

错误解决方法：在 Azure 门户的“防火墙”窗格中，使用虚拟网络规则控件为子网[添加虚拟网络规则](#anchor-how-to-by-using-firewall-portal-59j)。

### <a name="error-40615"></a>错误 40615

消息文本：“无法打开登录时请求的服务器 ‘{0}’。 不允许 IP 地址为 ‘{1}’ 的客户端访问此服务器。”

错误说明：客户端尝试从无权连接到服务器的 IP 地址进行连接。 服务器防火墙没有 IP 地址规则允许客户端从给定 IP 地址与数据库进行通信。

错误解决方法：输入客户端 IP 地址作为 IP 规则。 使用 Azure 门户中的“防火墙”窗格执行此步骤。

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>使用门户创建虚拟网络规则

本部分介绍了如何使用 [Azure 门户][http-azure-portal-link-ref-477t]在 Azure SQL 数据库中的数据库中创建虚拟网络规则。 此规则要求数据库接受来自已被标记为“虚拟网络服务终结点”的特定子网的通信。

> [!NOTE]
> 若要向服务器的虚拟网络防火墙规则添加服务终结点，请确保为子网启用服务终结点。
>
> 如果没有为子网启用服务终结点，门户会要求你启用它们。 在你添加规则的窗格中选择“启用”按钮。

## <a name="powershell-alternative"></a>PowerShell 备用

脚本还可以使用 PowerShell cmdlet New-AzSqlServerVirtualNetworkRule 或 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) 来创建虚拟网络规则。 如果有兴趣，可以参阅[使用 PowerShell 创建 Azure SQL 数据库的虚拟网络服务终结点和规则][sql-db-vnet-service-endpoint-rule-powershell-md-52d]。

## <a name="rest-api-alternative"></a>REST API 替代项

在内部，用于 SQL 虚拟网络操作的 PowerShell cmdlet 会调用 REST API。 可以直接调用 REST API。

- [虚拟网络规则：操作][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>先决条件

必须已经有一个子网使用特定的虚拟网络服务终结点类型名称进行标记，且该名称必须与 Azure SQL 数据库相关。

- 相关的终结点类型名称为 Microsoft.Sql。
- 如果子网可能未使用类型名称进行标记，请参阅[验证子网是否是终结点][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]。

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure 门户步骤

1. 登录到 [Azure 门户][http-azure-portal-link-ref-477t]。

1. 搜索并选择“SQL Server”，然后选择你的服务器。 在“安全性”下，选择“防火墙和虚拟网络” 。

1. 将“允许访问 Azure 服务”设置为“关闭”。 

    > [!IMPORTANT]
    > 如果将此控件设置为“开启”，则你的服务器会接受来自 Azure 边界内任何子网的通信。 该通信源自系统认定的 IP 地址之一，系统认定这些地址在为 Azure 数据中心定义的范围内。 从安全角度来看，将此控件设置为“开启”可能会导致过度访问。 与 SQL 数据库的虚拟网络规则功能结合在一起的 Microsoft Azure 虚拟网络服务终结点功能可以减少安全面。

1. 在“虚拟网络”部分选择“+ 添加现有项”。 

    ![屏幕截图显示了如何选择“+ 添加现有项”（子网终结点，作为 SQL 规则）。][image-portal-firewall-vnet-add-existing-10-png]

1. 在新的“创建/更新”窗格中，使用你的 Azure 资源的名称填写各个框。

    > [!TIP]
    > 必须包括子网的正确地址前缀。 可以在门户中找到“地址前缀”值。 转到“所有资源”&gt;“所有类型”&gt;“虚拟网络”。 筛选器会显示虚拟网络。 选择你的虚拟网络，然后选择“子网”。 “地址范围”列包含你需要的地址前缀。

    ![屏幕截图显示了如何填写新规则的各个框。][image-portal-firewall-create-update-vnet-rule-20-png]

1. 选择窗格底部的“确定”按钮。

1. 查看“防火墙”窗格中生成的虚拟网络规则。

    ![屏幕截图显示了“防火墙”窗格中的新规则。][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> 以下状态适用于这些规则：
>
> - 就绪：表示你启动的操作已成功。
> - **失败**：表示你启动的操作已失败。
> - 已删除：仅适用于删除操作，表示规则已被删除，不再应用。
> - **InProgress**：表示操作正在进行。 操作处于这种状态时，会应用旧规则。

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>相关文章

- [Azure 虚拟网络服务终结点][vm-virtual-network-service-endpoints-overview-649d]
- [服务器级和数据库级防火墙规则][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>后续步骤

- [使用 PowerShell 创建虚拟网络服务终结点，然后创建 Azure SQL 数据库的虚拟网络规则][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [虚拟网络规则：操作][rest-api-virtual-network-rules-operations-862r]（使用 REST API）

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
