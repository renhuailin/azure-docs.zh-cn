---
title: 将 Qualys 漏洞管理数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 Qualys 漏洞管理数据连接到 Azure Sentinel。
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
ms.openlocfilehash: 111db39a1822e47fb8a14e10d3833fd58153f5b5
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253433"
---
# <a name="connect-your-qualys-vm-to-azure-sentinel-with-azure-function"></a>使用 Azure Function 将 Qualys VM 连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Qualys VM 数据连接器目前为公共预览版。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

通过 Qualys 漏洞管理 (VM) 连接器，可以轻松将所有 [Qualys VM](https://www.qualys.com/apps/vulnerability-management/) 安全性解决方案日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报，并改进调查。 Qualys VM 与 Azure Sentinel 之间集成后，可通过 Azure Functions 使用 REST API 来拉取日志数据。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-qualys-vm"></a>配置并连接 Qualys VM

Azure Functions 可以集成 Qualys VM 并直接从中拉取事件和日志，还可以将其转发到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击“数据连接器”，并选择“Qualys 漏洞管理”连接器。

1. 选择“打开连接器页面”。

1. 按照“Qualys 漏洞管理”页上的说明进行操作。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 **QualysHostDetection_CL** 表下的 Log Analytics 中。

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍如何使用 Azure Function 应用将 Qualys VM 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。