---
title: 将 SRX 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 SRX 数据连接器将刺柏 SRX 日志拉入 Azure Sentinel。 查看工作簿中的 SRX 数据，创建警报，并改进调查。
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
ms.openlocfilehash: b10c47a31bf1be10c278d4d9e0dce633bc7bff6c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530631"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>将你的 SRX 防火墙连接到 Azure Sentinel

> [!IMPORTANT]
> 刺柏 SRX 连接器目前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

本文介绍了如何将 SRX 防火墙设备连接到 Azure Sentinel。 使用 SRX 数据连接器，可以轻松地将 SRX 日志与 Azure Sentinel 连接，使你可以查看工作簿中的数据，使用它创建自定义警报，并将其合并以改进调查。 SRX 和 Azure Sentinel 之间的集成利用 Syslog。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 你必须具有 Azure Sentinel 工作区的 "读取" 和 "写入" 权限。

- 你的刺柏 SRX 解决方案必须配置为通过 Syslog 导出日志。

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>将 SRX 日志转发到 Syslog 代理  

配置 SRX 以通过 Syslog 代理将 Syslog 消息转发到 Azure Sentinel 工作区。

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 " **数据连接器** " 库中，选择 " **)  (SRX** "，然后单击 " **连接器" 页面**。

1. 按照 " **刺柏 SRX** 连接器" 页上的说明进行操作：

    1. 安装并载入适用于 Linux 的代理

        -  (物理或虚拟) 选择 Azure Linux VM 或非 Azure Linux 计算机。

    1. 配置要收集的日志

        - 选择工作区代理配置中的功能和严重性。

    1. 配置并连接刺柏 SRX

        - 按照这些说明操作，将 SRX 配置为转发 syslog。
            - [流量日志 (安全策略日志) ](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [系统日志](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - 对于远程服务器，请使用安装 Linux 代理的 Linux 计算机的 IP 地址。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 Syslog 下的 Log Analytics 中。

请参阅连接器页中的 " **后续步骤** " 选项卡，了解一些有用的示例查询。

## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 SRX 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
