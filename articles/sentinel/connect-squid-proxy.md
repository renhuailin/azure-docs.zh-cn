---
title: 将 Squid Proxy 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Squid Proxy 数据连接器将 Squid 代理日志拉取到 Azure Sentinel。 在工作簿中查看 Squid Proxy 数据，创建警报，并改进调查。
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 4bf5d3c4a4ad70f5fc5f09c50144b759900370b6
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253412"
---
# <a name="connect-your-squid-proxy-to-azure-sentinel"></a>将 Squid Proxy 连接到 Azure Sentinel

> [!IMPORTANT]
> Squid Proxy 连接器目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍如何将 Squid 代理设备连接到 Azure Sentinel。 使用 Squid Proxy 数据连接器，可以轻松地将 Squid 日志与 Azure Sentinel 连接，这样，你就可以查看工作簿中的数据，将其用于创建自定义警报，并用它来改进调查。 Squid Proxy 和 Azure Sentinel 之间的集成利用 Log Analytics 代理提供的本地文件处理功能。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 必须对 Azure Sentinel 工作区拥有读取和写入权限。

## <a name="forward-squid-proxy-logs-to-the-log-analytics-agent"></a>将 Squid Proxy 日志转发到 Log Analytics 代理  

将 Squid Proxy 配置为通过 Log Analytics 代理将日志文件发送到 Azure 工作区。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从“数据连接器”库中，选择“Squid Proxy（预览版）”连接器，然后单击“打开连接器页”。

1. 按照 Squid Proxy 连接器页上的说明进行操作：

    1. 安装和载入适用于 Linux 的代理

        - 选择 Azure Linux VM 或非 Azure Linux 虚拟机（物理或虚拟）。

    1. 配置要收集的日志

        - 在工作区的高级设置中，添加自定义日志类型，上传示例文件，并按照说明进行配置。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 `SquidProxy_CL` 表中的“自定义日志”下的“日志”中。

请参阅连接器页中的“后续步骤”选项卡，了解一些有用的示例查询。

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。 

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Squid Proxy 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。