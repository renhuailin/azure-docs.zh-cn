---
title: PowerShell 脚本：创建 Azure 数据共享邀请
description: 此 PowerShell 脚本发送数据共享邀请。
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 5f65d081e724206c1c64ad08189d1b620bbb4f2c
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221326"
---
# <a name="use-a-powershell-script-to-monitor-the-usage-of-a-sent-data-share"></a>使用 PowerShell 脚本监视发送的数据共享的使用情况

此 PowerShell 脚本创建数据共享邀请。

## <a name="sample-script"></a>示例脚本


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 说明 |
|---|---|
| [新-AzDataShareInvitation](/powershell/module/az.datashare/new-azdatashareinvitation) | 创建数据共享邀请。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。

可以在 [Azure 数据共享 PowerShell 示例](../../samples-powershell.md)中找到其他 Azure 数据共享 PowerShell 脚本示例。
