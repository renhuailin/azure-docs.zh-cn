---
title: PowerShell 脚本：创建 Azure Cosmos DB Cassandra API 密钥空间和表
description: Azure PowerShell 脚本 - Azure Cosmos DB 创建 Cassandra API 密钥空间和表
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 3d5dec36481a57eadeb68d7ec3182cff76d6b081
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684414"
---
# <a name="create-a-keyspace-and-table-for-azure-cosmos-db---cassandra-api"></a>创建 Azure Cosmos DB 的密钥空间和表 - Cassandra API
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

本示例需要 Azure PowerShell Az 5.4.0 或更高版本。 运行 `Get-Module -ListAvailable Az`，查看已安装哪些版本。
如果需要安装，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

通过运行 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 登录到 Azure。

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-create.ps1 "Create a keyspace and table for Cassandra API")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
|**Azure Cosmos DB**| |
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | 创建 Cosmos DB 帐户。 |
| [New-AzCosmosDBCassandraKeyspace](/powershell/module/az.cosmosdb/new-azcosmosdbcassandrakeyspace) | 创建 Cosmos DB Cassandra API 密钥空间。 |
| [New-AzCosmosDBCassandraClusterKey](/powershell/module/az.cosmosdb/new-azcosmosdbcassandraclusterkey) | 创建 Cosmos DB Cassandra API 群集密钥。 |
| [New-AzCosmosDBCassandraColumn](/powershell/module/az.cosmosdb/new-azcosmosdbcassandracolumn) | 创建 Cosmos DB Cassandra API 列。 |
| [New-AzCosmosDBCassandraSchema](/powershell/module/az.cosmosdb/new-azcosmosdbcassandraschema) | 创建 Cosmos DB Cassandra API 架构。 |
| [New-AzCosmosDBCassandraTable](/powershell/module/az.cosmosdb/new-azcosmosdbcassandratable) | 创建 Cosmos DB Cassandra API 表。 |
|**Azure 资源组**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。