---
title: 将 Cisco 统一计算系统 (UCS) 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Cisco UCS 数据连接器将 Cisco UCS 日志拉取到 Azure Sentinel 中。 在工作簿中查看 Cisco UCS 数据、创建警报，并改进调查。
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
ms.openlocfilehash: d8ad48063ebeece77f476dfde5eb124f316540b9
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253268"
---
# <a name="connect-your-cisco-unified-computing-system-ucs-to-azure-sentinel"></a>将 Cisco 统一计算系统 (UCS) 连接到 Azure Sentinel

> [!IMPORTANT]
> Cisco UCS 连接器目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍了如何将 Cisco 统一计算系统 (UCS) 设备连接到 Azure Sentinel。 使用 Cisco UCS 数据连接器，可以轻松地将 UCS 日志与 Azure Sentinel 连接，这样就可以在工作簿中查看数据，将其用于创建自定义警报，并用它来改进调查。 Cisco UCS 和 Azure Sentinel 之间的集成会使用 Syslog。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

- Cisco UCS 解决方案必须配置为通过 Syslog 导出日志。

## <a name="forward-cisco-ucs-logs-to-the-syslog-agent"></a>将 Cisco UCS 日志转发到 Syslog 代理  

将 Cisco UCS 配置为通过 Syslog 代理将 Syslog 消息转发到 Azure Sentinel 工作区。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 在“数据连接器”库中，选择“Cisco UCS(预览)”连接器，然后单击“打开连接器页”  。

1. 按照“Cisco UCS”连接器页上的说明操作：

    1. 安装并加入适用于 Linux 的代理

        - 选择 Azure Linux VM 或非 Azure Linux 虚拟机（物理或虚拟）。

    1. 配置要收集的日志

        - 在工作区代理配置中选择设备和严重性。

    1. 配置并连接 Cisco UCS

        - 按照[以下说明](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog)操作，将 Cisco UCS 配置为转发 Syslog。 对于远程服务器，请使用安装了 Linux 代理的 Linux 计算机的 IP 地址。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据会显示在 Syslog 下的 Log Analytics 中。

请参阅连接器页中的“后续步骤”选项卡，了解一些有用的示例查询。

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

在本文档中，你学习了如何将 Cisco UCS 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。