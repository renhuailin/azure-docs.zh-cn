---
title: 将 VMware ESXi 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 VMware ESXi 数据连接器将 ESXi 日志请求到 Azure Sentinel。 查看工作簿中的 ESXi 数据、创建警报并改善调查。
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
ms.openlocfilehash: 3d478a9ac3cf91f3f6815859b8534efff88f07b1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700793"
---
# <a name="connect-your-vmware-esxi-to-azure-sentinel"></a>将 VMware ESXi 连接到 Azure Sentinel

> [!IMPORTANT]
> VMware ESXi 连接器目前处于 **预览阶段**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

本文介绍如何将 VMware ESXi 设备连接到 Azure Sentinel。 VMware ESXi 数据连接器可让你轻松地将 VMware ESXi 日志引入 Azure Sentinel，使你能够更深入地了解组织的 ESXi 活动，并帮助改进安全操作功能。 VMware ESXi 和 Azure Sentinel 之间的集成利用安装了 Log Analytics 代理的 Syslog 服务器。 它还使用基于 Kusto 函数的自定义的日志分析器。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 你必须具有 Azure Sentinel 工作区的 "读取" 和 "写入" 权限。

- 你的 VMware ESXi 解决方案必须配置为通过 Syslog 导出日志。

## <a name="send-vmware-esxi-logs-to-the-syslog-agent"></a>将 VMware ESXi 日志发送到 Syslog 代理  

配置 VMware ESXi 以通过 Syslog 代理将 Syslog 消息转发到 Azure Sentinel 工作区。
3. 按照 **VMware ESXi** 页面上的说明进行操作。


1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 " **数据连接器** " 库中，选择 " **VMware ESXi (预览")** "连接器"，然后单击 " **连接器" 页面**。

1. 按照 **VMware ESXi** 连接器 "页上的说明进行操作：

    1. 安装并载入适用于 Linux 的代理

        -  (物理或虚拟) 选择 Azure Linux VM 或非 Azure Linux 计算机。

    1. 配置要收集的日志

        - 选择 **工作区代理配置** 中的功能和严重性。

    1. 配置并连接 VMware ESXi

        - 按照以下说明配置用于转发 syslog 的 VMware ESXi：
            - [VMware ESXi 3.5 和4。x](https://kb.vmware.com/s/article/1016621)
            - [VMware ESXi 5.0 及更高版本](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)

            对于远程服务器，请使用安装 Linux 代理的 Linux 计算机的 IP 地址。

## <a name="validate-connectivity-and-find-your-data"></a>验证连接和查找数据

可能需要长达20分钟的时间才会开始在 Azure Sentinel 中显示日志。 

成功建立连接后，数据将 **显示在 "***日志管理*" 部分中的 "日志管理" 部分下的 " *Syslog* " 表中。

此数据连接器依赖于基于 Kusto 函数的分析器来按预期方式工作。 [按照以下步骤](https://aka.ms/sentinel-vmwareesxi-parser) 创建 **VMwareESXi** Kusto 函数别名。 然后，可以在 `VMwareESXi` Azure Sentinel 中键入任何查询窗口以查询数据。

请参阅连接器页中的 " **后续步骤** " 选项卡，了解一些有用的查询示例。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 VMware ESXi 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
