---
title: 将 VMware ESXi 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 VMware ESXi 数据连接器将 ESXi 日志拉取到 Azure Sentinel。 在工作簿中查看 ESXi 数据，创建警报，并改进调查。
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
ms.date: 03/01/2021
ms.author: yelevin
ms.openlocfilehash: 2f43607e47e059e6bba9a3ae02c07262ec7309a2
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252659"
---
# <a name="connect-your-vmware-esxi-to-azure-sentinel"></a>将 VMware ESXi 连接到 Azure Sentinel

> [!IMPORTANT]
> VMware ESXi 连接器目前处于预览阶段。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍了如何将 VMware ESXi 设备连接到 Azure Sentinel。 VMware ESXi 数据连接器可让你轻松地将 VMware ESXi 日志引入 Azure Sentinel，使你能够更深入地了解组织的 ESXi 活动，并帮助改进安全操作功能。 VMware ESXi 和 Azure Sentinel 的集成使用安装了 Log Analytics 代理的 Syslog 服务器。 它还使用了基于 Kusto 函数的定制日志分析程序。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 你必须具有对 Azure Sentinel 工作区的读取和写入权限。

- VMware ESXi 解决方案必须配置为通过 Syslog 导出日志。

## <a name="send-vmware-esxi-logs-to-the-syslog-agent"></a>将 VMware ESXi 日志发送到 Syslog 代理  

将 VMware ESXi 配置为通过 Syslog 代理将 Syslog 消息转发到 Azure Sentinel 工作区。
3. 按照 VMware ESXi 页面上的说明进行操作。


1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 在“数据连接器”库中，选择“VMware ESXi (预览版)”连接器，然后选择“打开连接器页”。

1. 按照 VMware ESXi 连接器页面上的说明进行操作：

    1. 安装并加入适用于 Linux 的代理

        - 选择 Azure Linux VM 或非 Azure Linux 虚拟机（物理或虚拟）。

    1. 配置要收集的日志

        - 在 **工作区代理配置** 中选择设备和严重性。

    1. 配置和连接 VMware ESXi

        - 按照以下说明操作，将 VMware ESXi 配置为转发 syslog：
            - [VMware ESXi 3.5 和 4.x](https://kb.vmware.com/s/article/1016621)
            - [VMware ESXi 5.0 及更高版本](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)

            对于远程服务器，请使用安装了 Linux 代理的 Linux 计算机的 IP 地址。

## <a name="validate-connectivity-and-find-your-data"></a>验证连接并查找数据

最长可能需要 20 分钟的时间，日志才会开始显示在 Azure Sentinel 中。 

在成功建立连接后，数据显示在“日志”中“日志管理”部分下的“Syslog”表内。

此数据连接器必须依赖于基于 Kusto 函数的分析程序才能按预期方式工作。 [按照这些步骤操作](https://aka.ms/sentinel-vmwareesxi-parser)，以创建 VMwareESXi Kusto 函数别名。 然后，可以在 Azure Sentinel 中的任何查询窗口键入 `VMwareESXi` 以查询数据。

请参阅连接器页中的“后续步骤”选项卡，了解一些有用的查询示例。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 VMware ESXi 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。