---
title: PowerShell 脚本：列出 Azure Data Share 中的现有共享
description: 此 PowerShell 脚本列出并显示共享的详细信息。
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 54b4b6cedc2dc09e4a9d5c4b7b937e80750e17c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220816"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>使用 PowerShell 查看已发送的数据共享的详细信息

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
| [Get-AzDataShare](/powershell/module/az.datashare/get-azdatashare) | 获取并列出帐户中的共享。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。

可以在 [Azure Data Share PowerShell 示例](../../samples-powershell.md)中找到其他 Azure Data Share PowerShell 脚本示例。
