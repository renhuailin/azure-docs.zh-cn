---
title: Exchange 对等互连演练
titleSuffix: Azure
description: Exchange 对等互连演练
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 97430d86cba1107ef67264a181925d2a4d7c12c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586816"
---
# <a name="exchange-peering-walkthrough"></a>Exchange 对等互连演练

本部分介绍了设置和管理 Exchange 对等互连所需执行的步骤。

## <a name="create-an-exchange-peering"></a>创建 Exchange 对等互连
> [!div class="mx-imgBorder"]
> ![Exchange 对等互连工作流和连接状态](./media/exchange-peering.png)

为了预配 Exchange 对等互连，必须遵循以下步骤：
1. 查看 Microsoft [对等互连策略](https://peering.azurewebsites.net/peering)，以了解 Exchange 对等互连的要求。
1. 在 [PeeringDB](https://www.peeringdb.com/net/694) 中查找 Microsoft 对等互连位置和对等互连设施 ID
1. 有关更多详细信息，请按照[使用 PowerShell 创建和修改 exchange 对等互连](howto-exchange-powershell.md)中的说明，向 Exchange 对等互连请求对等互连位置。
1. 提交对等互连请求后，Microsoft 将检查请求，并根据需要与你联系。
1. 请求批准后，连接状态将更改为“已批准”
1. 由你配置 BGP 会话，然后通知 Microsoft
1. 我们将预配包含 DENY ALL 策略的 BGP 会话，并进行端到端验证。
1. 如果成功，你将收到一条通知，指出对等互连的连接状态为“活动”。
1. 然后，将允许流量通过新的对等互连。

请注意，不要将连接状态与标准 [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) 会话状态混淆。

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>将旧版 Exchange 对等互连转换为 Azure 资源
若要将旧版 Exchange 对等互连转换为 Azure 资源，必须遵循以下步骤：
1. 请按照[将旧版 Exchange 对等互连转换为 Azure 资源](howto-legacy-exchange-powershell.md)中的说明进行操作
1. 提交转换请求后，Microsoft 将检查请求，并根据需要与你联系。
1. 批准后，将显示 Exchange 对等互连的连接状态为“活动”。

## <a name="deprovision-exchange-peering"></a>撤销 Exchange 对等互连
与 [Microsoft 对等互连](mailto:peering@microsoft.com)联系，以撤销 Exchange 对等互连。

当 Exchange 对等互连设置为撤销时，连接状态将显示为“PendingRemove”

> [!NOTE]
> 如果在连接状态为“ProvisioningStarted”或“ProvisioningCompleted”时，运行 PowerShell cmdlet 来删除 Exchange 对等互连，则操作将失败。

## <a name="next-steps"></a>后续步骤

* 了解[设置与 Microsoft 进行对等互连的先决条件](prerequisites.md)。
