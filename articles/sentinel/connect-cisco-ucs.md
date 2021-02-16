---
title: 将 Cisco 统一计算系统 (UCS) 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Cisco UCS 数据连接器将 Cisco UCS 日志请求到 Azure Sentinel。 查看工作簿中的 Cisco UCS 数据，创建警报，并改进调查。
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
ms.openlocfilehash: 15e31b8dc5ac6db5861e3ea0fb5390ddf0a8c945
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530665"
---
# <a name="connect-your-cisco-unified-computing-system-ucs-to-azure-sentinel"></a>将 Cisco 统一计算系统 (UCS) 连接到 Azure Sentinel

> [!IMPORTANT]
> Cisco UCS 连接器目前处于 **预览阶段**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

本文介绍如何将 Cisco 统一计算系统 (UCS) 设备连接到 Azure Sentinel。 Cisco UCS 数据连接器可让你轻松地使用 Azure Sentinel 连接 UCS 日志，这样你就可以查看工作簿中的数据，使用它创建自定义警报，并将其合并以改进调查。 Cisco UCS 和 Azure Sentinel 之间的集成利用 Syslog。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 你必须具有 Azure Sentinel 工作区的 "读取" 和 "写入" 权限。

- Cisco UCS 解决方案必须配置为通过 Syslog 导出日志。

## <a name="forward-cisco-ucs-logs-to-the-syslog-agent"></a>将 Cisco UCS 日志转发到 Syslog 代理  

将 Cisco UCS 配置为通过 Syslog 代理将 Syslog 消息转发到 Azure Sentinel 工作区。

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 " **数据连接器** " 库中，选择 " **Cisco UCS (预览")** 连接器，然后单击 " **连接器" 页面**。

1. 按照 **CISCO UCS** 连接器页面上的说明进行操作：

    1. 安装并载入适用于 Linux 的代理

        -  (物理或虚拟) 选择 Azure Linux VM 或非 Azure Linux 计算机。

    1. 配置要收集的日志

        - 选择工作区代理配置中的功能和严重性。

    1. 配置并连接 Cisco UCS

        - 按照 [这些说明操作](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) ，将 Cisco UCS 配置为转发 syslog。 对于远程服务器，请使用安装 Linux 代理的 Linux 计算机的 IP 地址。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 Syslog 下的 Log Analytics 中。

请参阅连接器页中的 " **后续步骤** " 选项卡，了解一些有用的示例查询。

## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Cisco UCS 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
