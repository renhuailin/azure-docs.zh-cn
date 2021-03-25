---
title: PowerShell 脚本：获取 Azure Cosmos DB 帐户的密钥和连接字符串操作
description: Azure PowerShell 脚本示例 - Azure Cosmos DB 帐户的帐户密钥和连接字符串操作
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: eb84684201ddf79f2a73fd1bcba1a0f69899bea8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684303"
---
# <a name="connection-string-and-account-key-operations-for-an-azure-cosmos-db-account-using-powershell"></a>使用 PowerShell 的 Azure Cosmos DB 帐户的连接字符串和帐户密钥操作
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

本示例需要 Azure PowerShell Az 5.4.0 或更高版本。 运行 `Get-Module -ListAvailable Az`，查看已安装哪些版本。
如果需要安装，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

通过运行 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 登录到 Azure。

## <a name="sample-script"></a>示例脚本

> [!NOTE]
> 此示例演示如何使用 SQL API 帐户。 若要将此示例用于其他 API，请复制相关属性，并将其应用于 API 特定的脚本

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-keys-connection-strings.ps1 "Connection strings and account keys for Azure Cosmos account")]

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
| [Get-AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/get-azcosmosdbaccountkey) | 获取 Cosmos DB 帐户的连接字符串或密钥（读写或只读）。 |
| [New-AzCosmosDBAccountKey](/powershell/module/az.cosmosdb/new-azcosmosdbaccountkey) | 为 Cosmos DB 帐户重新生成指定的密钥。 |
|**Azure 资源组**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组，包括所有嵌套的资源。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。