---
title: 将 Microsoft Defender for Endpoint data 连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Microsoft Defender for Endpoint (以前的 Microsoft Defender ATP) 数据连接到 Azure Sentinel。
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
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98623360"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>连接 Microsoft Defender for Endpoint (以前的 Microsoft Defender ATP) 的警报

> [!IMPORTANT]
>
> - **Microsoft defender For Endpoint** 以前称为 " **Microsoft Defender 高级威胁防护** " 或 **MDATP**。
>
>     你可能会看到产品中仍在使用旧名称 (将其数据连接器包含在 Azure Sentinel) 中一段时间。

[Microsoft defender For endpoint](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)连接器允许你将来自 microsoft Defender for endpoint 的警报流式传输到 Azure Sentinel。 这样，你就可以更全面地分析组织中的安全事件，并构建行动手册以实现有效且即时的响应。

> [!NOTE]
>
> 若要从 Microsoft Defender for Endpoint 的 [高级搜寻](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview)中引入新的原始数据日志，请使用新的连接器 Microsoft 365 Defender (以前的 Microsoft 威胁防护， [请参阅文档](./connect-microsoft-365-defender.md)) 。

## <a name="prerequisites"></a>必备条件

- 你必须具有 Microsoft Defender for Endpoint 的有效许可证，如为 [终结点部署设置 Microsoft defender](/windows/security/threat-protection/microsoft-defender-atp/licensing)中所述。 

- 你必须是 Azure Sentinel 租户上的全局管理员或安全管理员。

## <a name="connect-to-microsoft-defender-for-endpoint"></a>连接到 Microsoft Defender for Endpoint

如果已部署 Microsoft Defender for Endpoint 并引入你的数据，则可以轻松地将警报流式传输到 Azure Sentinel。

1. 在 Azure Sentinel 中，选择 " **数据连接器**"，选择 " **Microsoft defender for Endpoint** (仍会被从库中称为" *microsoft defender 高级威胁防护* ") ，然后选择" **打开连接器 "页面**。

1. 单击“连接”  。 

1. 若要在 **日志** 中查询 Microsoft Defender for Endpoint 警报，请在查询窗口中输入 **SecurityAlert** ，并添加 **提供程序名称** 为 **MDATP** 的筛选器。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Microsoft Defender for Endpoint 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./tutorial-detect-threats-built-in.md)。