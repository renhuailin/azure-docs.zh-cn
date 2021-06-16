---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2020
ms.author: rogarana
ms.custom: include file, devx-track-azurepowershell
ms.openlocfilehash: 75eef9053defcf4e57639fa6dc6a9b327fa65287
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110721984"
---
高级文件存储可用的 30 多个区域 **全部** 支持 NFS。

支持区域还在不断增加。 有关最新列表，请使用下面的示例查询支持 NFS 的区域列表。 还可以在“高级文件存储”下的[各区域的 Azure 产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all)页中查看区域支持。

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Provide specific subscription id if you want  list for a different subscription
$subscription = $azContext.Subscription.Id

# Invoke the REST API
$restUri = "https://management.azure.com/subscriptions/$subscription/providers/Microsoft.Storage/skus?api-version=2019-06-01"
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader

# List of all regions that has NFS support.
$response.value| Where-Object -FilterScript {$_.capabilities| Where-Object { $_.name -eq 'supportsNfsShare' -and $_.value -eq 'true'}}| Select-Object locations, kind, name

# List of regions that support NFS Zonal redundancy.
$response.value| Where-Object -FilterScript {($_.name -EQ 'Premium_ZRS') -and ($_.capabilities| Where-Object { $_.name -eq 'supportsNfsShare' -and $_.value -eq 'true'})}| Select-Object locations
```

示例响应
```
List of regions that support NFS Zonal redundancy
locations
---------
{eastus}
{eastus2}
{westeurope}
{southeastasia}
{japaneast}
{northeurope}
{australiaeast}
{westus2}
{uksouth}
{eastus2euap}
{francecentral}
```
