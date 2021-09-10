---
title: PowerShell 脚本：创建新的 Azure 数据共享
description: 此 PowerShell 脚本在现有数据共享帐户中创建新的数据共享。
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 36c1bdfef2afe4c34796a804784317a5e7fe12ff
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221343"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>使用 PowerShell 在 Azure 中创建数据共享

此 PowerShell 脚本在现有数据共享帐户中创建新的数据共享。

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
| [新-AzDataShare](/powershell/module/az.datashare/new-azdatashare) | 创建数据共享。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。

可以在 [Azure 数据共享 PowerShell 示例](../../samples-powershell.md)中找到其他 Azure 数据共享 PowerShell 脚本示例。
