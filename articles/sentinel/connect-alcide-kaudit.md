---
title: 将 Alcide kAudit 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Alcide kAudit 数据连接到 Azure Sentinel。
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
ms.openlocfilehash: 418a1289b6f584e58b88e2fb0f571f724ba5d800
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253187"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>将 Alcide kAudit 连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Alcide kAudit 数据连接器目前为公共预览版。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

[Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) 可帮助你识别 Kubernetes 的异常行为，并专注于 Kubernetes 违规和事件，同时减少检测时间。 本文介绍了如何将 Alcide kAudit 解决方案连接到 Azure Sentinel。 使用 Alcide kAudit 数据连接器，可以轻松地将 kAudit 日志数据引入 Azure Sentinel，这样，你就可以在工作簿中查看这些数据，将其用于创建自定义警报，并用它来改进调查。 Alcide kAudit 和 Azure Sentinel 之间的集成利用 REST API。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

- 必须有工作区的共享密钥的读取权限。

## <a name="configure-and-connect-alcide-kaudit"></a>配置并连接 Alcide kAudit

Alcide kAudit 可以将日志直接导出到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击导航菜单中的“数据连接器”。

1. 从库中选择“Alcide kAudit”，然后单击“打开连接器页”按钮 。

1. 请按照 [Alcide kAudit 安装指南](https://awesomeopensource.com/project/alcideio/kaudit?categoryPage=29#before-installing-alcide-kaudit)中的分步说明操作。

1. 系统要求提供工作区 ID 和主键时，你可以从 Alcide kAudit 数据连接器页面复制。

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="工作区 ID 和主键":::

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 CustomLogs 中的以下数据类型下的“日志”中 ：

- **alcide_kaudit_detections_1_CL** - Alcide kAudit 检测 
- **alcide_kaudit_activity_1_CL** - Alcide kAudit 活动日志
- **alcide_kaudit_selections_count_1_CL** - Alcide kAudit 活动数
- **alcide_kaudit_selections_details_1_CL** - Alcide kAudit 活动详细信息

若要将“日志”中的相关架构用于 Alcide kAudit，请搜索上述数据类型。

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Alcide kAudit 连接到 Azure Sentinel。 若要充分利用此数据连接器内置的功能，请单击“数据连接器”页上的“后续步骤”选项卡。 可以在其中找到一些现成的示例查询，以便开始查找有用的信息。

要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。