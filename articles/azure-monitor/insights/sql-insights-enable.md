---
title: 启用 SQL 见解
description: 在 Azure Monitor 中启用 SQL 见解
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: 9a5d14c3363f5d4b4d25e0592b184b6e706fef6b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734287"
---
# <a name="enable-sql-insights-preview"></a>启用 SQL 见解（预览版）
本文介绍如何启用 [SQL 见解](sql-insights-overview.md)来监视 SQL 部署。 通过连接到 SQL 部署的 Azure 虚拟机来进行监视，并且该虚拟机使用动态管理视图 (DMV) 来收集监视数据。 可以使用监视配置文件控制要收集的数据集和收集频率。

> [!NOTE]
> 若要通过使用资源管理器模板创建监视配置文件和虚拟机来启用 SQL 见解，请参阅 [SQL 见解的资源管理器模板示例](resource-manager-sql-insights.md)。

若要了解如何启用 SQL 见解，还可以观看此“公开的数据”剧集。
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/How-to-Set-up-Azure-Monitor-for-SQL-Insights/player?format=ny]

## <a name="create-log-analytics-workspace"></a>创建 Log Analytics 工作区
SQL 见解将其数据存储在一个或多个 [Log Analytics 工作区](../logs/data-platform-logs.md#log-analytics-and-workspaces)中。  在启用 SQL 见解前，需要[创建一个工作区](../logs/quick-create-workspace.md)或选择一个现有工作区。 单个工作区可使用多个监视配置文件，但工作区和配置文件必须位于同一 Azure 区域。 若要启用并访问 SQL 见解中的功能，必须在工作区中拥有 [Log Analytics 参与者](../logs/manage-access.md)角色。 

## <a name="create-monitoring-user"></a>创建监视用户 
需要在要监视的 SQL 部署中设置一个用户。 针对不同类型的 SQL 部署，请按照以下过程进行操作。

下面的说明按照 SQL 类型介绍了你可以监视的过程。  若要使用此脚本一次对多个 SQL 资源完成此任务，请参阅以下[自述文件](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation-README.txt)和[示例脚本](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation.ps1)。


### <a name="azure-sql-database"></a>Azure SQL 数据库

> [!NOTE]
> SQL 见解不支持以下 Azure SQL 数据库方案：
> - **弹性池**：无法收集弹性池的指标。 无法收集弹性池中数据库的指标。
> - **低服务层级**：无法收集基本、S0、S1 和 S2 [服务层级](../../azure-sql/database/resource-limits-dtu-single-databases.md)上的数据库的指标
> 
> SQL 见解对以下 Azure SQL 数据库方案的支持有限：
> - **无服务器层级**：可以收集使用[无服务器计算层级](../../azure-sql/database/serverless-tier-overview.md)的数据库的指标。 但是，指标收集过程会重置自动暂停延迟计时器，导致数据库无法进入“已自动暂停”状态

在 Azure 门户中使用 [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) 或[查询编辑器（预览版）](../../azure-sql/database/connect-query-portal.md)打开 Azure SQL 数据库。

运行以下脚本创建具有所需权限的用户。 请将 user 替换为用户名，将 mystrongpassword 替换为密码 。

```sql
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="创建 telegraf 用户脚本。" lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

验证是否已创建用户。

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="验证 telegraf 用户脚本。" lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type,
       authentication_type_desc as authentication_type
from sys.database_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

### <a name="azure-sql-managed-instance"></a>Azure SQL 托管实例
登录到 Azure SQL 托管实例，然后使用 [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) 或类似工具运行以下脚本，以创建具有所需权限的监视用户。 请将 user 替换为用户名，将 mystrongpassword 替换为密码 。

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO 
```

### <a name="sql-server"></a>SQL Server
登录到运行 SQL Server 的 Azure 虚拟机，并使用 [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) 或类似工具运行以下脚本，以创建具有所需权限的监视用户。 请将 user 替换为用户名，将 mystrongpassword 替换为密码 。

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

验证是否已创建用户。

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type
from sys.server_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

## <a name="create-azure-virtual-machine"></a>创建 Azure 虚拟机 
你需要创建一个或多个 Azure 虚拟机，这些虚拟机将用于收集监视 SQL 的数据。  

> [!NOTE]
>  [监视配置文件](#create-sql-monitoring-profile)指定要从要监视的不同类型的 SQL 中收集的数据。 每个监视虚拟机只能有一个监视配置文件与之关联。 如果需要使用多个监视配置文件，则需要为每个配置文件分别创建一个虚拟机。

### <a name="azure-virtual-machine-requirements"></a>Azure 虚拟机要求
Azure 虚拟机的要求如下。

- 操作系统：Ubuntu 18.04 
- 建议的 Azure 虚拟机大小：Standard_B2s（2 个 CPU、4 GiB 内存） 
- 支持的区域：[Azure Monitor 代理支持](../agents/azure-monitor-agent-overview.md#supported-regions)的任何区域

> [!NOTE]
> Standard_B2s（2 个 CPU、4 GiB 内存）虚拟机大小最多支持 100 个连接字符串。 一个虚拟机不得分配超过 100 个连接。

根据你的 SQL 资源的网络设置，可能需要将你的虚拟机放置在你的 SQL 资源所在的虚拟网络中，这样虚拟机便可以进行网络连接以收集监视数据。  

## <a name="configure-network-settings"></a>配置网络设置
每种类型的 SQL 都会提供监视虚拟机安全访问 SQL 的方法。  以下各部分介绍基于 SQL 类型的各选项。

### <a name="azure-sql-database"></a>Azure SQL 数据库

SQL 见解支持通过公共终结点以及虚拟网络访问 Azure SQL 数据库。

若要通过公共终结点访问，请在“防火墙设置”页和 [IP 防火墙设置](../../azure-sql/database/network-access-controls-overview.md#ip-firewall-rules)部分下添加一条规则。  若要指定通过虚拟网络访问，可以设置[虚拟网络防火墙规则](../../azure-sql/database/network-access-controls-overview.md#virtual-network-firewall-rules)，并设置 [Azure Monitor 代理所需的服务标记](../agents/azure-monitor-agent-overview.md#networking)。  [本文](../../azure-sql/database/network-access-controls-overview.md#ip-vs-virtual-network-firewall-rules)介绍这两种类型的防火墙规则之间的差异。

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="设置服务器防火墙" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="防火墙设置。" lightbox="media/sql-insights-enable/firewall-settings.png":::


### <a name="azure-sql-managed-instance"></a>Azure SQL 托管实例

如果监视虚拟机将与 SQL MI 资源位于同一 VNet 中，请参阅[在同一 VNet 内连接](../../azure-sql/managed-instance/connect-application-instance.md#connect-inside-the-same-vnet)。 如果监视虚拟机将与 SQL MI 资源位于不同 VNet 中，请参阅[在不同 VNet 内连接](../../azure-sql/managed-instance/connect-application-instance.md#connect-inside-a-different-vnet)。


### <a name="sql-server"></a>SQL Server 
如果监视虚拟机与 SQL 虚拟机资源位于同一 VNet 中，请参阅[在虚拟网络中连接到 SQL Server](../../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md#connect-to-sql-server-within-a-virtual-network)。 如果监视虚拟机与 SQL 虚拟机资源位于不同 VNet 中，请参阅[通过 Internet 连接到 SQL Server](../../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md#connect-to-sql-server-over-the-internet)。

## <a name="store-monitoring-password-in-key-vault"></a>在密钥保管库中存储监视密码
应在密钥保管库中存储 SQL 用户连接密码，而不是将它们直接输入监视配置文件连接字符串中。

设置用于 SQL 监视的配置文件时，需要对要使用的密钥保管库资源具有以下权限之一：

- Microsoft.Authorization/roleAssignments/write 
- Microsoft.Authorization/roleAssignments/delete 权限，例如用户访问管理员或所有者 

在创建使用指定的密钥保管库的 SQL Monitoring 配置文件时，将自动新建一条访问策略。 对于“密钥保管库网络”设置，请使用“允许从所有网络访问”。


## <a name="create-sql-monitoring-profile"></a>创建 SQL 监视配置文件
打开 SQL 见解：在 Azure 门户中“Azure Monitor”菜单的“见解”部分选择“SQL (预览版)”。   点击“新建配置文件”。 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="新建配置文件。" lightbox="media/sql-insights-enable/create-new-profile.png":::

该配置文件将存储要从 SQL 系统收集的信息。  它对以下各项有特定设置： 

- Azure SQL 数据库 
- Azure SQL 托管实例
- 虚拟机上运行的 SQL Server  

例如，可以创建两个配置文件，一个名为“SQL 生产”，一个名为“SQL 暂存”，对于数据收集频率、要收集的数据以及数据发送到哪个工作区，它们有不同的设置 。 

此配置文件作为[数据收集规则](../agents/data-collection-rule-overview.md)资源存储在所选订阅和资源组中。 每个配置文件都需要以下各项：

- 名称： 创建后不能编辑。
- 位置。 指的是 Azure 区域。
- 用于存储监视数据的 Log Analytics 工作区。
- 关于要收集的 SQL 监视数据的频率和类型的集合设置。

> [!NOTE]
> 配置文件的位置应与计划向其发送监视数据的 Log Analytics 工作区位于同一位置。


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="配置文件详细信息。" lightbox="media/sql-insights-enable/profile-details.png":::

输入监视配置文件的详细信息后，请单击“创建监视配置文件”。 部署配置文件最长可能需要一分钟时间。  如果未看到“监视配置文件”组合框中列出新的配置文件，请单击刷新按钮，配置文件应会在部署完成后立即显示。  选择新的配置文件后，选择“管理配置文件”选项卡，添加要与配置文件关联的监视计算机。

### <a name="add-monitoring-machine"></a>添加监视计算机
选择“添加监视计算机”，打开上下文面板，选择要设置的用于监视 SQL 实例的虚拟机，并提供连接字符串。

选择监视虚拟机的订阅和名称。 如果要使用密钥保管库来存储监视用户的密码，请选择包含这些机密的密钥保管库资源，然后输入要在连接字符串中使用的 URL 和机密名称。 请查看下一节，详细了解如何确定用于不同 SQL 部署的连接字符串。


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="添加监视计算机。" lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>添加连接字符串 
连接字符串指定在登录到 SQL 以运行动态管理视图时 SQL 见解应使用的用户名。 如果要使用密钥保管库来存储监视用户的密码，请提供要使用的 URL 和机密名称。 

连接字符串根据 SQL 资源类型而异：

#### <a name="azure-sql-database"></a>Azure SQL 数据库
在窗体中输入连接字符串：

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

从数据库的“连接字符串”菜单项获取详细信息。

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="SQL 数据库连接字符串" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

若要监视可读辅助数据库，请在连接字符串中包含键值 `ApplicationIntent=ReadOnly`。 SQL 见解支持监视单个辅助数据库。 收集的数据将被标记，以反映主数据库或辅助数据库。 

#### <a name="azure-sql-managed-instance"></a>Azure SQL 托管实例
在窗体中输入连接字符串：

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
从托管实例的“连接字符串”菜单项获取详细信息。


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="SQL 托管实例连接字符串" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

若要监视可读辅助数据库，请在连接字符串中包含键值 `ApplicationIntent=ReadOnly`。 SQL 见解支持监视单个辅助数据库，收集的数据将被标记为反映主数据库或辅助数据库。 

#### <a name="sql-server"></a>SQL Server 
在窗体中输入连接字符串：

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

如果监视虚拟机位于同一 VNET 中，请使用服务器的专用 IP 地址。  否则，请使用公共 IP 地址。 如果使用的是 Azure SQL 虚拟机，可以在资源的“安全”页中查看此处使用的端口。

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="SQL 虚拟机安全" lightbox="media/sql-insights-enable/sql-vm-security.png":::

## <a name="monitoring-profile-created"></a>已创建监视配置文件 

选择“添加监视虚拟机”，将虚拟机配置为从 SQL 资源收集数据。 不要返回到“概述”选项卡。 几分钟后，“状态”列应更改为“正在收集”，应会看到已选择监视的 SQL 资源的数据。

如果看不到数据，请参阅 [SQL 见解疑难解答](sql-insights-troubleshoot.md)，确定发生了什么问题。 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="已创建配置文件" lightbox="media/sql-insights-enable/profile-created.png":::

> [!NOTE]
> 如果需要在监视 VM 上更新监视配置文件或连接字符串，可以通过 SQL 见解的“管理配置文件”选项卡来执行此操作。保存更新后，将在大约 5 分钟内应用更改。

## <a name="next-steps"></a>后续步骤

- 如果 SQL 见解在启用后无法正常工作，请参阅 [SQL 见解疑难解答](sql-insights-troubleshoot.md)。
