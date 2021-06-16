---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file, devx-track-azurepowershell
ms.openlocfilehash: d53bfaab5e00b95be52f652b72e9d55a76c396a6
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110720464"
---
以下 PowerShell 命令将拒绝发往存储帐户公共终结点的所有流量。 请注意，此命令的 `-Bypass` 参数设置为 `AzureServices`。 这会允许受信任的第一方服务（例如 Azure 文件同步）通过公共终结点访问存储帐户。

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```
