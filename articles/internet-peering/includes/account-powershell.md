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
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "81678491"
---
在开始配置之前，请安装并导入所需的模块。 你需要具有管理员权限才能在 PowerShell 中安装模块。

1. 安装并导入 Az 模块。
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. 安装并导入 Az.Peering 模块。
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. 使用此命令验证是否已正确导入模块：
    ```powershell
    Get-Module
    ```
1. 使用以下命令登录到 Azure 帐户：
    ```powershell
    Connect-AzAccount
    ```
1. 查看帐户的订阅，并选择要在其中创建对等互连的订阅。
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. 如果尚未有资源组，则在创建对等互连之前，必须先创建一个资源组。 为此，可以运行以下命令：

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> 如果尚未关联 ASN 和订阅，请按照[关联对等 ASN](../howto-subscription-association-powershell.md) 中的步骤进行操作。 请求对等互连时需要此操作。

> [!NOTE]
> 资源组的位置与选择设置对等互连的位置无关。
&nbsp;
