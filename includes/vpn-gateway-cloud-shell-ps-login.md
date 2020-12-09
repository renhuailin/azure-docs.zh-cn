---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 24d146da7946176c92902698d0f52ae01baf79ee
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061585"
---
如果在本地运行 PowerShell，请用提升的权限打开 PowerShell 控制台，并连接到 Azure 帐户。 Connect-AzureRmAccount cmdlet 会提示输入凭据。 进行身份验证后，它会下载帐户设置，以便 Azure PowerShell 可以使用这些设置。

如果使用 Azure Cloud Shell 而不是在本地运行 PowerShell，则会注意到不需要运行 *AzAccount*。 选择 " **试用**" 后，Azure Cloud Shell 自动连接到 Azure 帐户。

1. 如果在本地运行 PowerShell，请登录。

   ```azurepowershell
   Connect-AzAccount
   ```

1. 如果有多个订阅，请获取 Azure 订阅的列表。

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. 指定要使用的订阅。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
