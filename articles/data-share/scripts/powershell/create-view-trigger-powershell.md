---
title: PowerShell 脚本：创建和查看 Azure 数据共享快照触发器
description: 此 PowerShell 脚本创建并获取共享快照触发器。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 05b70641873ab352ca92ac199d99c05b2fe0f4e6
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221292"
---
# <a name="use-powershell-to-create-and-share-snapshot-triggers"></a>使用 PowerShell 创建和共享快照触发器

此 PowerShell 脚本创建并获取共享快照触发器。

## <a name="sample-script"></a>示例脚本

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$subscriptionName = "<Share subscription name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new snapshot trigger
New-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName $subscriptionName -Name $dataShareName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List snapshot triggers
Get-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName $subscriptionName  -Name $dataShareName

#Get a specific share snapshot trigger
Get-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName -Name $dataShareName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 说明 |
|---|---|
| [新-AzDataShareTrigger](/powershell/module/az.datashare/new-azdatasharetrigger) | 创建共享快照触发器。 |
| [AzDataShareTrigger](/powershell/module/az.datashare/get-azdatasharesynchronizationsetting) | 获取共享同步的同步设置。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。

可以在 [Azure 数据共享 PowerShell 示例](../../samples-powershell.md)中找到其他 Azure 数据共享 PowerShell 脚本示例。
