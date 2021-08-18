---
title: 将 Microsoft Defender for Identity（前身为 Azure ATP）的数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何一键将日志从 Microsoft Defender for Identity（前身为 Azure 高级威胁防护）(ATP) 流式传输到 Azure Sentinel。
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: c7dea620058fa91945448e9966120db514eb286b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778748"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>从 Microsoft Defender For Identity（原 Azure 高级威胁防护）连接数据

> [!IMPORTANT]
> Azure Sentinel 中的 Microsoft Defender for Identity 数据连接器目前为公共预览版。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]


本文介绍如何将安全警报从 [Microsoft Defender for Identity](/azure-advanced-threat-protection/what-is-atp) 流式传输到 Azure Sentinel。 

若要转发运行状况警报以及安全警报，请将 Microsoft Defender for Identity 与 Syslog 服务器集成。 有关详细信息，请参阅 [Microsoft Defender for Identity 文档](/defender-for-identity/setting-syslog)。 

## <a name="prerequisites"></a>必备知识

- 必须有具备全局管理员或安全管理员权限的用户
- 你必须是 Microsoft Defender for Identity 的预览客户，并启用 Microsoft Defender for Identity 与 Microsoft Cloud App Security 之间的集成。 有关详细信息，请参阅 [Microsoft Defender for Identity 集成](/cloud-app-security/mdi-integration)。

## <a name="connect-to-microsoft-defender-for-identity"></a>连接到 Microsoft Defender for Identity

确保[网络上已启用](/azure-advanced-threat-protection/install-atp-step1) Microsoft Defender for Identity 预览版本。
如果 Microsoft Defender for Identity 已部署且正在引入你的数据，那么可轻松地将可疑警报流式传输到 Azure Sentinel。 警报最多可能需要 24 小时才能开始流式传输到 Azure Sentinel。


1. 若要将 Microsoft Defender for Identity 连接到 Azure Sentinel，则必须先启用 Microsoft Defender for Identity 与 Microsoft Cloud App Security 之间的集成。 有关如何执行此操作的信息，请参阅 [Microsoft Defender for Identity 集成](/cloud-app-security/mdi-integration)。

1. 在 Azure Sentinel 中，选择“数据连接器”，然后单击“Microsoft Defender for Identity(预览版)”磁贴 。

1. 可以选择是否希望 Microsoft Defender for Identity 中的警报在 Azure Sentinel 中自动生成事件。 在“创建事件”下选择“启用”， 以便启用默认的分析规则，这样，当连接的安全服务中生成警报时，就会自动创建事件。 然后，可以在“分析”下的“活动规则”中编辑此规则。 

1. 单击“连接”  。

1. 若要使用 Log Analytics 中的相关架构以获得 Microsoft Defender for Identity 警报，请搜索“SecurityAlert”。

> [!NOTE]
> 如果警报大于 30 KB，则 Azure Sentinel 将停止在警报中显示“实体”字段。

## <a name="next-steps"></a>后续步骤
在本文档中，你学习了如何将 Microsoft Defender for Identity 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
