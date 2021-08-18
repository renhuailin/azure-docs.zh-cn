---
title: 将 Perimeter 81 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Perimeter 81 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: 07c850cf98ffddf01cb4479affdb62d30a5bb522
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253883"
---
# <a name="connect-your-perimeter-81-activity-logs-to-azure-sentinel"></a>将 Perimeter 81 活动日志连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Perimeter 81 数据连接器目前处于公共预览版阶段。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍如何将 [Perimeter 81 活动日志](https://www.perimeter81.com/)设备连接到 Azure Sentinel。 使用 Perimeter 81 活动日志数据连接器，可以轻松地将 Perimeter 81 数据引入 Azure Sentinel，这样，你就可以在工作簿中查看这些数据，将其用于创建自定义警报，并用它来改进调查。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

- 必须有工作区的共享密钥的读取权限。

## <a name="configure-and-connect-perimeter-81-activity-logs"></a>配置和连接 Perimeter 81 活动日志

Perimeter 81 活动日志可以直接将日志集成和导出到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击导航菜单中的“数据连接器”。

1. 从库中选择“Perimeter 81 活动日志”，然后单击“打开连接器页”按钮 。

1. 从“Perimeter 81 活动日志”连接器页上，复制“工作区 ID”和“主密钥”并将它们粘贴到 Perimeter 81，[如此处所示](https://support.perimeter81.com/hc/en-us/articles/360012680780) 。

1. 在完成说明中的操作后，会在 Azure Sentinel 连接器页中看到已连接的数据类型。

## <a name="find-your-data"></a>查找数据

在成功建立连接后，数据将显示在“CustomLogs” - “Perimeter81_CL”下的“日志”中  。

可能最长会需要 20 分钟，日志才会开始显示。

## <a name="next-steps"></a>后续步骤

在本文档中，你已了解了如何将 Perimeter 81 活动日志连接到 Azure Sentinel。 若要充分利用此数据连接器内置的功能，请单击“数据连接器”页上的“后续步骤”选项卡。 其中会有一个现成的工作簿和一些示例查询，这样就可以开始查找有用信息了。

要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。