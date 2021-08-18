---
title: 将 Cloud App Security 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Microsoft Cloud App Security (MCAS) 连接器，将警报和 Cloud Discovery 日志从 MCAS 流式传输到 Azure Sentinel。
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
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 18dcd909ef55894e7548129a9b5836b68a30efe0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777994"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>通过 Microsoft Cloud App Security 连接数据 

使用 [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) 连接器，可以将警报和 [Cloud Discovery 日志](/cloud-app-security/tutorial-shadow-it)从 MCAS 流式传输到 Azure Sentinel。 这样，用户则可以查看云应用，获得复杂的分析来识别和应对网络威胁，并控制数据的传输方式。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>先决条件

- 用户必须对工作区具有读取和写入权限。
- 用户必须对工作区的租户具有全局管理员或安全管理员权限。
- 要将 Cloud Discovery 日志流式传输到 Azure Sentinel，请[在 Microsoft Cloud App Security 中启用 Azure Sentinel 作为 SIEM](/cloud-app-security/siem-sentinel)。

> [!IMPORTANT]
> 引入 Cloud Discovery 日志当前为公共预览版。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="connect-to-cloud-app-security"></a>连接到 Cloud App Security

如果已有 Cloud App Security，请确保已[在网络上启用](/cloud-app-security/getting-started-with-cloud-app-security)它。
如果已部署 Cloud App Security 并引入数据，则可以轻松将警报数据流式传输到 Azure Sentinel。


1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。 在连接器列表中，单击“Microsoft Cloud App Security”图块，然后单击右下角的“打开连接器页”按钮。

1. 选择要将哪些日志流式传输到 Azure Sentinel，可以选择“警报”和“Cloud Discovery 日志”（预览版）。 

1. 单击“应用更改”。

1. 可以选择是否希望 Cloud App Security 中的警报在 Azure Sentinel 中自动生成事件。 在“创建事件 - 建议！”下，选择“启用”以启用根据警报自动创建事件的默认分析规则。 然后，可以在“分析”下的“活动规则”选项卡中编辑此规则。 

1. 要对 Cloud App Security 警报使用 Log Analytics 中的相关架构，请在查询窗口中键入 `SecurityAlert`。 对于 Cloud Discovery 日志架构，请键入 `McasShadowItReporting`。

> [!NOTE]
> Cloud Discovery 通过整合底层用户连接云应用的数据，有助于检测和识别趋势。
>
> 由于 Cloud Discovery 数据按天整合，因此请注意，Azure Sentinel 中不会反映几小时（最长 24 小时）的最新数据。 如有低层调查要求更即时的数据，应直接在原始数据所在的源设备或服务中执行此操作。

## <a name="next-steps"></a>后续步骤
在本文档中，你已了解如何将 Microsoft Cloud App Security 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 使用[内置](./detect-threats-built-in.md)或[自定义](detect-threats-custom.md)规则开始通过 Azure Sentinel 检测威胁。