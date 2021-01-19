---
title: 将 Squid 代理数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Squid 代理数据连接器将 Squid 代理日志请求到 Azure Sentinel。 在工作簿中查看 Squid 代理数据，创建警报，并改进调查。
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
ms.openlocfilehash: b183abf8d42e6f4b1c43db2d87b2650721e0c2a9
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567879"
---
# <a name="connect-your-squid-proxy-to-azure-sentinel"></a>将 Squid 代理连接到 Azure Sentinel

> [!IMPORTANT]
> Squid 代理连接器当前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

本文介绍如何将 Squid 代理设备连接到 Azure Sentinel。 使用 Squid 代理数据连接器，可以轻松地将你的 Squid 日志与 Azure Sentinel 连接，这样你就可以查看工作簿中的数据，使用它创建自定义警报，并将其合并以改进调查。 Squid 代理与 Azure Sentinel 之间的集成利用 Syslog。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 你必须具有 Azure Sentinel 工作区的 "读取" 和 "写入" 权限。

## <a name="forward-squid-proxy-logs-to-the-syslog-agent"></a>将 Squid Proxy 日志转发到 Syslog 代理  

将 Squid Proxy 配置为通过 Syslog 代理将 Syslog 消息转发到 Azure 工作区。

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 **数据连接器** 库中，选择 " **Squid Proxy (预览")** 连接器，然后单击 " **连接器" 页面**。

1. 按照 " **Squid 代理** 连接器" 页上的说明进行操作：

    1. 安装并载入适用于 Linux 的代理

        -  (物理或虚拟) 选择 Azure Linux VM 或非 Azure Linux 计算机。

    1. 配置要收集的日志

        - 在工作区的 "高级设置" 中，添加自定义日志类型，上传示例文件，并将配置为定向。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据会显示在表中的 "**自定义日志**" 下面的 "**日志**" 中 `SquidProxy_CL` 。

请参阅连接器页中的 " **后续步骤** " 选项卡，了解一些有用的示例查询。

## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。 

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Squid 代理连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
