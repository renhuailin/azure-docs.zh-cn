---
title: PowerShell 脚本：接受来自 Azure Data Share 的邀请
description: 此 PowerShell 脚本接受来自某个现有的数据共享的邀请。
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8ce2a315512af65147fa2354c5895bcff94710cf
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110697152"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>使用 PowerShell 获取数据共享邀请

此 PowerShell 脚本接受发送到使用者的邀请。

## <a name="sample-script"></a>示例脚本
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 说明 |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | 获取并列出已发送的数据共享邀请。 |
| [New-AzDataShareSubscription](/powershell/module/az.datashare/get-azdatasharesubscription) | 创建数据共享订阅。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。

可以在 [Azure Data Share PowerShell 示例](../../samples-powershell.md)中找到其他 Azure Data Share PowerShell 脚本示例。
