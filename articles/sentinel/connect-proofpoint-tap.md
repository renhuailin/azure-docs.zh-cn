---
title: 将 Proofpoint Targeted Attack Protection (TAP) 数据连接到 Azure Sentinel| Microsoft Docs
description: 了解如何将 Proofpoint Targeted Attack Protection (TAP) 数据连接到 Azure Sentinel。
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 674ed9818b91636432609c8b79d3932a95c065f5
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254109"
---
# <a name="connect-your-proofpoint-tap-to-azure-sentinel-with-azure-function"></a>使用 Azure Function 将 Proofpoint TAP 连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Proofpoint TAP 数据连接器目前为公共预览版。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Proofpoint Targeted Attack Protection (TAP) 连接器可以让你轻松地将所有 [Proofpoint TAP](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection) 安全解决方案日志连接到 Azure Sentinel，以查看仪表板、创建自定义警报和改进调查。 Proofpoint TAP 与 Azure Sentinel 之间集成后，可通过 Azure Functions 使用 REST API 来拉取日志数据。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区所在的地理位置。

## <a name="configure-and-connect-proofpoint-tap"></a>配置并连接 Proofpoint TAP

Azure Functions 可以集成 Proofpoint TAP 并直接从中拉取事件和日志，还可以将其转发到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击“数据连接器”，然后选择“Proofpoint TAP”连接器。

1. 选择“打开连接器页面”。

1. 按照 Proofpoint TAP 页面上的说明操作。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 ProofpointTAPMessagesBlocked_CL、ProofpointTAPMessagesDelivered_CL、ProofpointTAPClicksPermitted_CL 和 ProofpointTAPClicksBlocked_CL    表下的 Log Analytics 中。

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

在本文档中，你学习了如何使用 Azure Function 应用将 Proofpoint TAP 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。