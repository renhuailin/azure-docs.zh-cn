---
title: PowerShell 脚本：创建新的 Azure Data Share
description: 此 PowerShell 脚本在现有 Data Share 帐户中创建新的数据共享。
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3a3d4c85f5063623abd8d86304efcc3bcc375d22
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110704091"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>使用 PowerShell 在 Azure 中创建 Data Share

此 PowerShell 脚本在现有 Data Share 帐户中创建新的 Data Share。

## <a name="sample-script"></a>示例脚本

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 说明 |
|---|---|
| [New-AzDataShare](/powershell/module/az.datashare/new-azdatashare) | 创建数据共享。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。

可以在 [Azure Data Share PowerShell 示例](../../samples-powershell.md)中找到其他 Azure Data Share PowerShell 脚本示例。
