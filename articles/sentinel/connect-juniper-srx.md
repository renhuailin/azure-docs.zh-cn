---
title: 将 Juniper Networks SRX 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Juniper SRX 数据连接器将 Juniper SRX 日志拉取到 Azure Sentinel 中。 在工作簿中查看 Juniper SRX 数据，创建警报，并改进调查。
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
ms.openlocfilehash: 39df2be86f4ca1d4410396b86aabee9a749ade92
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253588"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>将 Juniper SRX 防火墙连接到 Azure Sentinel

> [!IMPORTANT]
> Juniper SRX 连接器目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍如何将 Juniper SRX 防火墙设备连接到 Azure Sentinel。 使用 Juniper SRX 数据连接器，可以轻松地将 SRX 日志与 Azure Sentinel 连接，这样就可以在工作簿中查看数据，将其用于创建自定义警报，并用它来改进调查。 Juniper SRX 和 Azure Sentinel 之间的集成会使用 Syslog。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

- Juniper SRX 解决方案必须配置为通过 Syslog 导出日志。

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>将 Juniper SRX 日志转发到 Syslog 代理  

将 Juniper SRX 配置为通过 Syslog 代理将 Syslog 消息转发到 Azure Sentinel 工作区。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 在“数据连接器”库中，选择“Juniper SRX (预览版)”连接器，然后选择“打开连接器页”。

1. 按照“Juniper SRX”连接器页上的说明进行操作：

    1. 安装并加入适用于 Linux 的代理

        - 选择 Azure Linux VM 或非 Azure Linux 虚拟机（物理或虚拟）。

    1. 配置要收集的日志

        - 在工作区代理配置中选择设备和严重性。

    1. 配置和连接 Juniper SRX

        - 按照以下说明操作，将 Juniper SRX 配置为转发 Syslog。
            - [流量日志（安全策略日志）](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [系统日志](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - 对于远程服务器，请使用安装了 Linux 代理的 Linux 计算机的 IP 地址。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据会显示在 Syslog 下的 Log Analytics 中。

请参阅连接器页中的“后续步骤”选项卡，了解一些有用的示例查询。

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Juniper SRX 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。