---
title: PowerShell 脚本：设置和查看 Azure 数据共享同步设置
description: 此 PowerShell 脚本设置并获取共享同步设置。
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 401754436739e5594c0d7baac6507f9d9984fc31
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221275"
---
# <a name="use-powershell-to-set-azure-data-share-synchronization-settings"></a>使用 PowerShell 设置 Azure 数据共享同步设置

此 PowerShell 脚本设置并获取共享同步设置。

## <a name="sample-script"></a>示例脚本


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$settingName = "<Synchronization setting name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new synchronization setting
New-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $settingName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List share synchronization settings
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName 

#Get a specific share synchronization setting
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName -Name $settingName  
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 说明 |
|---|---|
| [新-AzDataShareSynchronizationSetting](/powershell/module/az.datashare/new-azdatasharesynchronizationsetting) | 创建共享同步。 |
| [AzDataShareSynchronizationSetting](/powershell/module/az.datashare/get-azdatasharesynchronizationsetting) | 获取共享同步的同步设置。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。

可以在 [Azure 数据共享 PowerShell 示例](../../samples-powershell.md)中找到其他 Azure 数据共享 PowerShell 脚本示例。
