---
title: 在 Azure Sentinel 中连接 DNS 数据 | Microsoft Docs
description: 了解如何通过在 DNS 计算机上安装代理，将任何在 Windows 上运行的域名服务器 (DNS) 连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: f4e6ddf4e3db0213587a9154a424813c3cc5ca83
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183137"
---
# <a name="connect-your-domain-name-server"></a>连接域名服务器

> [!IMPORTANT]
> Azure Sentinel 中的 DNS 数据连接器目前为公共预览版。
> 此功能不附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

> [!CAUTION]
> 启用 DNS 解决方案可能会导致目标计算机上的 CPU 使用率过高。 如上所述，建议不要在生产设置中使用此连接器。 我们正在努力开发此连接器的可正式发布的稳定版本。

可以将在 Windows 上运行的任何域名服务器 (DNS) 连接到 Azure Sentinel。 这通过在 DNS 计算机上安装代理来完成。 使用 DNS 日志，可以通过收集、分析和关联分析和审核日志以及来自 DNS 服务器的其他相关数据，获取对组织的 DNS 基础结构的安全性、性能和操作相关的见解。

启用 DNS 日志连接后，可以：
- 确定尝试解决恶意域名的客户端
- 确定过时的资源记录
- 确定经常查询的域名和请求频繁的 DNS 客户端
- 查看 DNS 服务器上的请求负载
- 查看动态 DNS 注册错误

## <a name="connected-sources"></a>连接的源

下表介绍了该解决方案支持的连接的源：

| **连接的源** | **支持** | **说明** |
| --- | --- | --- |
| [Windows 代理](../azure-monitor/agents/agent-windows.md) | 是 | 解决方案会从 Windows 代理收集 DNS 信息。 |
| [Linux 代理](../azure-monitor/vm/monitor-virtual-machine.md) | 否 | 解决方案不会从直接 Linux 代理收集 DNS 信息。 |
| [System Center Operations Manager 管理组](../azure-monitor/agents/om-agents.md) | 是 | 解决方案会从连接的 Operations Manager 管理组中的代理收集 DNS 信息。 从 Operations Manager 代理到 Azure Monitor 的直接连接不是必需的。 数据将从管理组转发到 Log Analytics 工作区。 |
| [Azure 存储帐户](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) | 否 | 解决方案不会使用 Azure 存储。 |

### <a name="data-collection-details"></a>数据收集详细信息

解决方案从安装有 Log Analytics 代理的 DNS 服务器收集 DNS 清单以及与 DNS 事件相关的数据。 通过运行 DNS PowerShell cmdlet 收集与清单相关的数据，如 DNS 服务器的数量、区域和资源记录。 该数据每两天更新一次。 与事件相关的数据几乎是从由 Windows Server 2012 R2 中增强的 DNS 日志记录和诊断提供的[分析和审核日志](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc)中实时收集的。


## <a name="connect-your-dns-appliance"></a>连接 DNS 设备

1. 在 Azure Sentinel 门户中，选择“数据连接器”，然后选择“DNS (预览)”磁贴 。
1. 如果 DNS 计算机位于 Azure 中：
    1. 单击“在 Azure Windows 虚拟机上安装代理”。
    1. 在“虚拟机”列表中，选择要流式传输到 Azure Sentinel 的 DNS 计算机。 请确保这是一个 Windows VM。
    1. 在为该 VM 打开的窗口中，单击“连接”。  
    1. 在“DNS 连接器”窗口中，单击“启用” 。 

2. 如果 DNS 计算机不是 Azure VM：
    1. 单击“在非 Azure 计算机上安装代理”。
    1. 在“直接代理”窗口中，选择“下载 Windows 代理(64 位)”或“下载 Windows 代理(32 位)”  。
    1. 在 DNS 计算机上安装代理。 复制“工作区 ID”、“主密钥”和“辅助密钥”，并在安装过程中出现提示时使用它们  。

3. 若要将 Log Analytics 中的相关架构用于 DNS 日志，请搜索“DnsEvents”。

## <a name="validate"></a>验证 

在 Log Analytics 中，搜索架构“DnsEvents”并确保存在事件。

## <a name="troubleshooting"></a>疑难解答

如果未在 Azure Sentinel 中显示查找查询，请执行以下步骤，以使查询正确显示：
1. 启用[服务器上的 DNS Analytics 日志](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11))。
2. 确保 DNSEvents 显示在 Log Analytics 集合列表中。
3. 启用 [Azure DNS Analytics](../azure-monitor/insights/dns-analytics.md)。
4. 在 Azure DNS Analytics 中的“配置”下，更改任何设置并保存，然后根据需要将其改回原始设置，然后再次保存。
5. 检查 Azure DNS Analytics，确保现在正在显示查询。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何将 DNS 本地设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。