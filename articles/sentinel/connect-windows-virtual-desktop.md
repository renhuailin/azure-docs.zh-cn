---
title: 将 Windows 虚拟桌面连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Windows 虚拟桌面数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2021
ms.author: bagol
ms.openlocfilehash: de7126b7a095868680ff08fab6691782379bb742
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952335"
---
# <a name="connect-windows-virtual-desktop-data-to-azure-sentinel"></a>将 Windows 虚拟桌面数据连接到 Azure Sentinel

本文介绍如何使用 Azure Sentinel 监视 Windows 虚拟桌面 (WVD) 环境。

例如，通过监视 Windows 虚拟桌面环境，你可以使用虚拟化桌面提供更多远程工作，同时还能维护组织的安全状态。

## <a name="windows-virtual-desktop-data-in-azure-sentinel"></a>Azure Sentinel 中的 Windows 虚拟桌面数据

Azure Sentinel 中的 Windows 虚拟桌面数据包括以下类型：


|数据  |说明  |
|---------|---------|
|**Windows 事件日志**     |  对于 WVD 环境中的 windows 事件日志，它会采用与其他 Windows 计算机（位于 WVD 环境外）上的 Windows 事件日志相同的方式流入已启用 Azure Sentinel 的 Log Analytics 工作区。 <br><br>将 Log Analytics 代理安装到 Windows 计算机上，并配置要发送到 Log Analytics 工作区的 Windows 事件日志。<br><br>有关详细信息，请参阅：<br>- [在 Windows 计算机上安装 Log Analytics 代理](../azure-monitor/agents/agent-windows.md)<br>- [使用 Log Analytics 代理收集 Windows 事件日志数据源](../azure-monitor/agents/data-sources-windows-events.md)<br>- [连接 Windows 安全事件](connect-windows-security-events.md)       |
|Microsoft Defender for Endpoint 警报     |  若要为 Windows 虚拟桌面配置 Defender for Endpoint，请使用与其他任何 Windows 终结点相同的配置过程。 <br><br>有关详细信息，请参阅： <br>- [设置 Microsoft Defender for Endpoint 部署](/windows/security/threat-protection/microsoft-defender-atp/production-deployment)<br>- [将 Microsoft 365 Defender 的数据连接到 Azure Sentinel](connect-microsoft-365-defender.md)       |
|**Windows 虚拟桌面诊断**     | Windows 虚拟桌面诊断是 Windows 虚拟桌面 PaaS 服务的一个功能，每当分配 Windows 虚拟桌面角色的用户使用此服务时，PaaS 服务都会记录信息。 <br><br>每个日志都包含有关活动中涉及的 Windows 虚拟桌面角色的信息，还包括在会话过程中出现的任何错误消息，以及租户信息和用户信息。 <br><br>诊断功能用于为用户和管理操作创建活动日志。 <br><br>有关详细信息，请参阅[在 Windows 虚拟桌面中使用 Log Analytics 诊断功能](../virtual-desktop/virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md)。        |
|     |         |

## <a name="connect-windows-virtual-desktop-data"></a>连接 Windows 虚拟桌面数据

若要开始将 Windows 虚拟桌面数据引入 Azure Sentinel，请使用 Windows 虚拟桌面文档中的说明。

有关详细信息，请参阅[将 Windows 虚拟桌面数据推送到 Log Analytics 工作区](../virtual-desktop/diagnostics-log-analytics.md)。

## <a name="find-your-data"></a>查找数据

成功建立连接后，请根据 Log Analytics 数据在 Azure Sentinel 中运行查询。

例如，请参阅 [Windows 虚拟桌面文档](../virtual-desktop/diagnostics-log-analytics.md)中的查询示例。


Azure Sentinel 还会在“常规” > “日志” > “WINDOWS 虚拟桌面”区域提供内置查询：

[![Azure Sentinel 中的 Windows 虚拟桌面内置查询](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png)](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png#lightbox)。

## <a name="next-steps"></a>后续步骤


有关详细信息，请参阅[适用于 Windows 虚拟桌面的 Azure Monitor 术语表](../virtual-desktop/azure-monitor-glossary.md)。