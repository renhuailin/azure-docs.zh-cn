---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 27da28073b01c6bc43868172d6c989d9518d8f53
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557180"
---
## <a name="start-your-powershell-session"></a>启动 PowerShell 会话
 

运行 [**Connect-Az Account**](/powershell/module/Az.Accounts/Connect-AzAccount) cmdlet，然后便会看到可输入凭据的登录屏幕。 使用与登录 Azure 门户相同的凭据。

```powershell
Connect-AzAccount
```

如果有多个订阅，请使用 [**Set-AzContext**](/powershell/module/az.accounts/set-azcontext) cmdlet 选择 PowerShell 会话应使用的订阅。 若要查看当前 PowerShell 会话正在使用哪个订阅，请运行 [**Get-AzContext**](/powershell/module/az.accounts/get-azcontext)。 若要查看所有订阅，请运行 [**Get-AzSubscription**](/powershell/module/az.accounts/get-azsubscription)。

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```