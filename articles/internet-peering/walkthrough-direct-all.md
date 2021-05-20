---
title: 直接对等互连演练
titleSuffix: Azure
description: 直接对等互连演练
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 6a1fa7f582511a2089bab2694f9f5ec47634ff54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592375"
---
# <a name="direct-peering-walkthrough"></a>直接对等互连演练

本部分介绍了设置和管理直接对等互连所需执行的步骤。

## <a name="create-a-direct-peering"></a>创建直接对等互连
> [!div class="mx-imgBorder"]
> ![直接对等互连工作流和连接状态](./media/direct-peering.png)

为了预配直接对等互连，必须遵循以下步骤：
1. 查看 Microsoft [对等互连策略](https://peering.azurewebsites.net/peering)，以了解直接对等互连的要求。
1. 按照[创建或修改直接对等互连](howto-direct-powershell.md)中的说明提交对等互连请求。
1. 当你提交对等互连请求后，Microsoft 将使用你的注册电子邮件地址与你联系，以提供 LOA（授权书）或寻求其他信息。
1. 对等互连请求获得批准后，连接状态将更改为 ProvisioningStarted。
1. 你需要：
    1. 根据 LOA 完成连接
    1. （可选）使用 169.254.0.0/16 执行链接测试
    1. 配置 BGP 会话，然后通知我们。
1. Microsoft 将使用 DENY ALL 策略预配 BGP 会话，并进行端到端验证。
1. 如果成功，你将收到一条通知，指出对等互连的连接状态为“活动”。
1. 然后，将允许流量通过新的对等互连。

请注意，不要将连接状态与标准 [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) 会话状态混淆。

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>将旧版直接对等互连转换为 Azure 资源
若要将旧版直接对等互连转换为 Azure 资源，必须遵循以下步骤：
1. 请按照[将旧版直接对等互连转换为 Azure 资源](howto-legacy-direct-powershell.md)中的说明进行操作
1. 提交转换请求后，Microsoft 将检查请求，并根据需要与你联系。
1. 批准后，将显示直接对等互连的连接状态为“活动”。

## <a name="deprovision-direct-peering"></a>取消设置直接对等互连
请与 [Microsoft 对等互连](mailto:peering@microsoft.com)团队联系以取消设置直接对等互连。

当直接对等互连设置为取消设置时，连接状态将显示为“PendingRemove”

> [!NOTE]
> 当连接状态为“ProvisioningStarted”或“ProvisioningCompleted”时，如果运行 PowerShell cmdlet 来删除直接对等互连，则操作将失败。

## <a name="next-steps"></a>后续步骤

* 了解[设置与 Microsoft 进行对等互连的先决条件](prerequisites.md)。
