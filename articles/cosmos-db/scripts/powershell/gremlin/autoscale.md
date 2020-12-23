---
title: 用于创建 Azure Cosmos DB Gremlin API 数据库和图（具有自动缩放功能）的 PowerShell 脚本
description: Azure PowerShell 脚本 - Azure Cosmos DB 创建 Gremlin API 数据库和图（具有自动缩放功能）
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 638c57960b9607c65092e657666f7a0bae375ff9
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099464"
---
# <a name="create-a-database-and-graph-with-autoscale-for-azure-cosmos-db---gremlin-api"></a>为 Azure Cosmos DB 创建数据库和图（具有自动缩放功能）- Gremlin API
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-autoscale.ps1 "Create a database and graph with autoscale for Gremlin API")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
|**Azure Cosmos DB**| |
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | 创建 Cosmos DB 帐户。 |
| [New-AzCosmosDBGremlinDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbgremlindatabase) | 创建 Gremlin API 数据库。 |
| [New-AzCosmosDBGremlinGraph](/powershell/module/az.cosmosdb/new-azcosmosdbgremlingraph) | 创建 Gremlin API 图。 |
|**Azure 资源组**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。
