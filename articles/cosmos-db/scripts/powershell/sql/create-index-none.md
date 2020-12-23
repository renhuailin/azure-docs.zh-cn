---
title: PowerShell 脚本：在 Azure Cosmos DB 帐户中创建一个索引关闭的容器
description: Azure PowerShell 脚本示例 - 在 Azure Cosmos DB 帐户中创建一个索引关闭的容器
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: bf3aa4844d990becc27ede222f2649ccbd3ab91b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089604"
---
# <a name="create-a-container-with-indexing-turned-off-in-an-azure-cosmos-db-account-using-powershell"></a>使用 PowerShell 在 Azure Cosmos DB 帐户中创建一个索引关闭的容器
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-container-create-index-none.ps1 "Create a container with indexing turned off in an Azure Cosmos DB account")]

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
| [New-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | 创建 Cosmos DB SQL 数据库。 |
| [New-AzCosmosDBSqlIndexingPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | 创建一个 PSSqlIndexingPolicy 对象，用作 New-AzCosmosDBSqlContainer 的参数。 |
| [New-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | 创建 Cosmos DB SQL 容器。 |
|**Azure 资源组**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。