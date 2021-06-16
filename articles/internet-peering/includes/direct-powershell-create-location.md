---
title: include 文件
titleSuffix: Azure
description: include 文件
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 65f282a63e95530549bd0db35c2f42fe5c25993d
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110720957"
---
PowerShell cmdlet Get-AzPeeringLocation 返回具有必需参数 `Kind` 的对等互连位置的列表，你将在后续步骤中用到该参数。

```powershell
Get-AzPeeringLocation -Kind Direct
```

直接对等互连位置包含以下字段：
* PeeringLocation 
* Country
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

请参阅 [PeeringDB](https://wwww.peeringdb.com)，验证你是否在所需的对等互连设施中。

此示例演示如何使用西雅图作为对等互连位置创建直接对等互连。

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```
