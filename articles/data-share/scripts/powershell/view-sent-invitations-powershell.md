---
title: PowerShell 脚本：列出发送到使用者的 Azure 数据共享邀请
description: 了解此 PowerShell 脚本如何获取发送到使用者的邀请，并查看可使用的脚本示例。
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 5f75894094fa2a15dbc9e2809ed6c3631df96c3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221207"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>使用 PowerShell 获取数据共享邀请

此 PowerShell 脚本将获取发送到使用者的邀请。

## <a name="sample-script"></a>示例脚本
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 说明 |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | 获取并列出已发送的数据共享邀请。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。

可以在 [Azure Data Share PowerShell 示例](../../samples-powershell.md)中找到其他 Azure Data Share PowerShell 脚本示例。
