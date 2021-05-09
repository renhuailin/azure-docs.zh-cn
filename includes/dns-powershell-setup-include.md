---
title: 包含适用于 Azure DNS 的 Azure PowerShell 的文件
description: 包含适用于 Azure DNS 的 Azure PowerShell 的文件
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 04/28//2021
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS
ms.openlocfilehash: 3634850a78db5ba03624a4363bbb72397460af38
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108203247"
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>设置适用于 Azure DNS 的 Azure PowerShell

### <a name="before-you-begin"></a>开始之前

[!INCLUDE [requires-azurerm](requires-azurerm.md)]

在开始配置之前，请确保具备以下各项。

* Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
* 需安装最新版本的 Azure 资源管理器 PowerShell cmdlet。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

### <a name="sign-in-to-your-azure-account"></a>登录到 Azure 帐户

打开 PowerShell 控制台并连接到帐户。 有关详细信息，请参阅[使用 Azure PowerShell 登录](/powershell/azure/azurerm/authenticate-azureps)。

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="select-the-subscription"></a>选择订阅
 
检查该帐户的订阅。

```azurepowershell-interactive
Get-AzSubscription
```

选择要使用的 Azure 订阅。

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>创建资源组

Azure Resource Manager 要求所有资源组指定一个位置。 此位置将用作该资源组中的资源的默认位置。 但是，由于所有 DNS 资源都是全局性而非区域性的，因此资源组位置的选择不会影响 Azure DNS。

如果使用现有资源组，可跳过此步骤。

```azurepowershell-interactive
New-AzResourceGroup -Name MyDNSResourceGroup -location "West US"
```
