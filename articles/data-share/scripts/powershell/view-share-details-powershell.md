---
title: PowerShell 脚本：列出 Azure 数据共享中的现有共享
description: 此 PowerShell 脚本列出并显示共享的详细信息。
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 54b4b6cedc2dc09e4a9d5c4b7b937e80750e17c6
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220816"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>使用 PowerShell 查看已发送数据共享的详细信息

此 PowerShell 脚本列出现有帐户中的数据共享，并获取特定共享的详细信息。


## <a name="sample-script"></a>示例脚本

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

#Lists all data shares within an account
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName

#Gets details of a specific data share
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 说明 |
|---|---|
| [AzDataShare](/powershell/module/az.datashare/get-azdatashare) | 获取帐户中的共享列表。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。

可以在 [Azure 数据共享 PowerShell 示例](../../samples-powershell.md)中找到其他 Azure 数据共享 PowerShell 脚本示例。
