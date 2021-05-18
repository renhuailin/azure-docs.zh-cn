---
title: 将 Microsoft Defender for Endpoint 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Microsoft Defender for Endpoint（以前称为 Microsoft Defender ATP）数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 0db4e0fe0472c75f1eae392980ae697f53007244
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98623360"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>连接来自 Microsoft Defender for Endpoint（以前称为 Microsoft Defender ATP）的警报

> [!IMPORTANT]
>
> - Microsoft Defender for Endpoint 以前被称为 Microsoft Defender 高级威胁防护 (MDATP)  。
>
>     此产品可能会在一段时间内使用旧名称（包括其在 Azure Sentinel 中的数据连接器）。

通过 [Microsoft Defender for Endpoint](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 连接器，可将来自 Microsoft Defender for Endpoint 的警报流式传输到 Azure Sentinel。 这样，就可更全面地分析组织中的安全事件，并构建 playbook 来实现有效且即时的响应。

> [!NOTE]
>
> 若要从 Microsoft Defender for Endpoint 的[高级搜寻](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview)引入新的原始数据日志，请使用 Microsoft 365 Defender（以前称为 Microsoft 威胁防护，具体请[查看文档](./connect-microsoft-365-defender.md)）的新版连接器。

## <a name="prerequisites"></a>先决条件

- 必须具有 Microsoft Defender for Endpoint 的有效许可证，如[设置 Microsoft Defender for Endpoint 部署](/windows/security/threat-protection/microsoft-defender-atp/licensing)中所述。 

- 必须是 Azure Sentinel 租户的全局管理员或安全管理员。

## <a name="connect-to-microsoft-defender-for-endpoint"></a>连接到 Microsoft Defender for Endpoint

如果 Microsoft Defender for Endpoint 已部署且正在引入你的数据，那么可轻松地将警报流式传输到 Azure Sentinel。

1. 在 Azure Sentinel 中，选择“数据连接器”，从库中选择 Microsoft Defender for Endpoint（可能仍被称为 Microsoft Defender 高级威胁防护），然后选择“打开连接器 ”页面 。

1. 单击“连接”  。 

1. 若要在日志中查询 Microsoft Defender for Endpoint 警报，请在查询窗口中输入“SecurityAlert”，并添加提供程序名称为 MDATP 的筛选器   。

## <a name="next-steps"></a>后续步骤
在本文档中，你学习了如何将 Microsoft Defender for Endpoint 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./tutorial-detect-threats-built-in.md)。