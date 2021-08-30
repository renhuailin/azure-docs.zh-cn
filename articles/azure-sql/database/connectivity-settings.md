---
title: Azure SQL 数据库和 Azure Synapse Analytics 的连接设置
description: 本文介绍传输层安全性 (TLS) 版本选择以及 Azure SQL 数据库和 Azure Synapse Analytics 的代理与重定向设置。
services: sql-database
ms.service: sql-database
ms.subservice: connect
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: mathoma, vanto
ms.date: 08/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 77b629f5fb354ee94d06055999ece59aea05e592
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750146"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL 连接设置
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

本文介绍的设置可用于控制 Azure Synapse Analytics 中与 Azure SQL 数据库和[专用 SQL 池（以前的 SQL DW）](../../synapse-analytics\sql-data-warehouse\sql-data-warehouse-overview-what-is.md)的服务器的连接。 这些设置适用于所有与服务器关联的 SQL 数据库和专用 SQL 池（以前的 SQL DW）数据库。

> [!IMPORTANT]
> 本文不适用于 Azure SQL 托管实例。

可从“防火墙和虚拟网络”屏幕访问连接设置，如以下屏幕截图所示：

:::image type="content" source="media/single-database-create-quickstart/manage-connectivity-settings.png" alt-text="SQL Server 的 Azure 门户中的防火墙和虚拟网络设置的屏幕截图":::

> [!NOTE]
> 这些设置在应用后会立即生效。 如果客户不满足每个设置的要求，则可能会遇到连接丢失的情况。

## <a name="deny-public-network-access"></a>拒绝公用网络访问

当将“拒绝公用网络访问”设置为“是”时，只允许通过专用终结点进行连接。 当该设置为“否”（默认值）时，客户可以使用公共终结点（使用基于 IP 的防火墙规则或基于虚拟网络的防火墙规则）或专用终结点（使用 Azure 专用链接）进行连接，如[网络访问概述](network-access-controls-overview.md)中所述。

 ![显示当“拒绝公用网络访问”分别设置为“是”和“否”时连接的关系图。][2]

如果逻辑服务器中没有任何现有的专用终结点，则尝试将“拒绝公用网络访问”设置为“是”时会失败，并显示如下类似错误消息：   

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server.
Please set up private endpoints and retry the operation.
```

> [!NOTE]
> 若要在已配置了专用终结点的逻辑服务器中定义虚拟网络防火墙规则，请将“拒绝公用网络访问”设置为“否”。

当将“拒绝公用网络访问”设置为“是”时，只允许通过专用终结点进行连接。 将拒绝通过公共终结点进行的所有连接，并显示如下类似错误信息：  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

当将“拒绝公用网络访问”设置为“是”时，将拒绝添加或更新防火墙规则的所有尝试，并显示如下类似错误信息：

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>通过 PowerShell 更改公共网络访问

> [!IMPORTANT]
> Azure SQL 数据库仍然支持 PowerShell Azure 资源管理器模块，但所有未来开发都针对 Az.Sql 模块。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 以下脚本需要 [Azure PowerShell 模块](/powershell/azure/install-az-ps)。

下面的 PowerShell 脚本展示了如何在服务器级别 `Get` 和 `Set`“公用网络访问”属性：

```powershell
# Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>通过 CLI 更改公共网络访问

> [!IMPORTANT]
> 本部分中的所有脚本都需要 [Azure CLI](/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>Bash shell 中的 Azure CLI

以下 CLI 脚本演示如何在 Bash shell 中更改“公用网络访问”设置：

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"
```

## <a name="minimal-tls-version"></a>最低 TLS 版本 

借助[最低传输层安全性 (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) 版本设置，客户可以选择其 SQL 数据库使用哪个 TLS 版本。

目前，我们支持 TLS 1.0、1.1 和 1.2。 设置最低 TLS 版本可确保支持更新的 TLS 版本。 例如，如果选择 TLS 版本 1.1，表示仅接受使用 TLS 1.1 和 1.2 的连接，并拒绝使用 TLS 1.0 的连接。 在测试以确认应用程序支持它后，建议将最低 TLS 版本设置为 1.2。 此版本包括对以前版本中漏洞的修复，以及 Azure SQL 数据库中支持的最高 TLS 版本。

> [!IMPORTANT]
> 最低 TLS 版本的默认设置为允许使用所有版本。 在设置为 TLS 的某个版本后，不能还原为默认值。

如果客户使用的应用程序依赖于更低的 TLS 版本，我们建议根据应用程序的要求设置最低 TLS 版本。 如果客户依赖于使用未加密连接进行连接的应用程序，我们建议不要设置任何最低 TLS 版本。

有关详细信息，请参阅 [SQL 数据库连接的 TLS 注意事项](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)。

设置最低 TLS 版本后，如果客户所使用服务器的 TLS 版本低于最低 TLS 版本，则其登录尝试将失败并显示以下错误：

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-the-minimal-tls-version-in-azure-portal"></a>在 Azure 门户中设置最低 TLS 版本

在 [Azure 门户](https://portal.azure.com)中，转到你的 SQL Server 资源。 在“安全”设置下，选择“防火墙和虚拟网络” 。 选择与服务器关联的所有 SQL 数据库所需的最低 TLS 版本，然后选择“保存” 。

## <a name="set-the-minimal-tls-version-via-powershell"></a>通过 PowerShell 设置最低 TLS 版本

> [!IMPORTANT]
> Azure SQL 数据库仍然支持 PowerShell Azure 资源管理器模块，但所有未来开发都针对 Az.Sql 模块。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 以下脚本需要 [Azure PowerShell 模块](/powershell/azure/install-az-ps)。

以下 PowerShell 脚本演示如何在逻辑服务器级别 `Get` 和 `Set`“最低 TLS 版本”属性：

```powershell
# Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-the-minimal-tls-version-via-the-azure-cli"></a>通过 Azure CLI 设置最低 TLS 版本

> [!IMPORTANT]
> 本部分中的所有脚本都需要 [Azure CLI](/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>Bash shell 中的 Azure CLI

以下 CLI 脚本演示如何在 Bash shell 中更改“最低 TLS 版本”设置：

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```

## <a name="change-the-connection-policy"></a>更改连接策略

[连接策略](connectivity-architecture.md#connection-policy)确定客户连接到 Azure SQL 数据库的方式。

## <a name="change-the-connection-policy-via-powershell"></a>通过 PowerShell 更改连接策略

> [!IMPORTANT]
> Azure SQL 数据库仍然支持 PowerShell Azure 资源管理器模块，但所有未来开发都针对 Az.Sql 模块。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 以下脚本需要 [Azure PowerShell 模块](/powershell/azure/install-az-ps)。

以下 PowerShell 脚本演示如何使用 PowerShell 更改连接策略：

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-the-connection-policy-via-the-azure-cli"></a>通过 Azure CLI 更改连接策略

> [!IMPORTANT]
> 本部分中的所有脚本都需要 [Azure CLI](/cli/azure/install-azure-cli)。

### <a name="azure-cli-in-a-bash-shell"></a>Bash shell 中的 Azure CLI

以下 CLI 脚本演示如何在 Bash shell 中更改连接策略：

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Windows 命令提示符中的 Azure CLI

以下 CLI 脚本演示如何从 Windows 命令提示符（安装了 Azure CLI）更改连接策略：

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>后续步骤

- 若要大致了解 Azure SQL 数据库中的连接工作原理，请参阅[连接体系结构](connectivity-architecture.md)。
- 有关如何更改服务器的连接策略的信息，请参阅 [conn-policy](/cli/azure/sql/server/conn-policy)。

<!--Image references-->
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
