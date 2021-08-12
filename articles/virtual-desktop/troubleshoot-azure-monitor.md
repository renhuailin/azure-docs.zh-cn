---
title: 排查适用于 Azure 虚拟桌面的 Azure Monitor 的问题 - Azure
description: 如何排查适用于 Azure 虚拟桌面的 Azure Monitor 的问题。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 27234cab8ebd6beefdd3766b3dca234ba5b41515
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111755350"
---
# <a name="troubleshoot-azure-monitor-for-azure-virtual-desktop"></a>适用于 Azure 虚拟桌面的 Azure Monitor 故障排除

本文介绍适用于 Azure 虚拟桌面的 Azure Monitor 的已知问题和常见问题解决方法。

## <a name="issues-with-configuration-and-setup"></a>配置和设置方面的问题

如果配置工作簿无法正常自动完成设置，可使用以下资源来手动设置环境：

- 若要手动启用诊断或访问 Log Analytics 工作区，请参阅[将 Azure 虚拟桌面诊断发送到 Log Analytics](diagnostics-log-analytics.md)。
- 若要在会话主机上手动安装 Log Analytics 扩展，请参阅[适用于 Windows 的 Log Analytics 虚拟机扩展](../virtual-machines/extensions/oms-windows.md)。
- 若要设置新 Log Analytics 工作区，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)。
- 若要添加、删除或编辑性能计数器，请参阅[配置性能计数器](../azure-monitor/agents/data-sources-performance-counters.md)。
- 若要为 Log Analytics 工作区配置 Windows 事件日志，请参阅[使用 Log Analytics 代理收集 Windows 事件日志数据源](../azure-monitor/agents/data-sources-windows-events.md)。

## <a name="my-data-isnt-displaying-properly"></a>我的数据未正确显示

如果你的数据未正确显示，请检查以下常用解决方法：

- 首先，请确保按照[使用适用于 Azure 虚拟桌面的 Azure Monitor 监视部署](azure-monitor.md)中的说明，使用配置工作簿正确进行设置。 如果缺少任何计数器或事件，则与它们关联的数据不会出现在 Azure 门户中。
- 请检查访问权限并联系资源所有者以请求缺少的权限；任何监视 Azure 虚拟桌面的人都需要以下权限：
    - 对保存 Azure 虚拟桌面资源的 Azure 订阅的读取访问权限
    - 对保存 Azure 虚拟桌面会话主机的订阅的资源组的读取访问权限 
    - 对你所使用的任何 Log Analytics 工作区的读访问权
- 你可能需要在服务器的防火墙中打开传出端口，以允许 Azure Monitor 和 Log Analytics 向门户发送数据。 若要了解如何执行此操作，请参阅下列文章：
      - [Azure Monitor 传出端口](../azure-monitor/app/ip-addresses.md)
      - [Log Analytics 防火墙要求](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements)。 
- 从最近的活动中未看到数据？ 你可能需要等待 15 分钟，然后刷新该源。 Azure Monitor 有 15 分钟的延迟期，因为需要填充日志数据。 有关详细信息，请参阅 [Azure Monitor 中的日志数据引入时间](../azure-monitor/logs/data-ingestion-time.md)。

如果没有缺少任何信息，但数据仍未正确显示，则可能是查询或数据源存在问题。 查看[已知问题和限制](#known-issues-and-limitations)。 

## <a name="i-want-to-customize-azure-monitor-for-azure-virtual-desktop"></a>我需要自定义适用于 Azure 虚拟桌面的 Azure Monitor

适用于 Azure 虚拟桌面的 Azure Monitor 使用 Azure Monitor 工作簿。 使用工作簿，可以保存 Azure 虚拟桌面工作簿模板的副本并制作自己的自定义内容。

按照设计，自定义工作簿模板将不会自动采用产品组中的更新。 有关详细信息，请参阅[排查基于工作簿的见解的问题](../azure-monitor/insights/troubleshoot-workbooks.md)和[工作簿概述](../azure-monitor/visualize/workbooks-overview.md)。

## <a name="i-cant-interpret-the-data"></a>我无法解释数据

请在 [Azure Monitor for Windows Virtual Desktop 词汇表](azure-monitor-glossary.md)中详细了解数据术语。

## <a name="the-data-i-need-isnt-available"></a>我需要的数据不可用

如果要监视更多性能计数器或 Windows 事件日志，可以允许它们将诊断信息发送到 Log Analytics 工作区，并在“主机诊断：主机浏览器”中进行监视。 

- 若要添加性能计数器，请参阅[配置性能计数器](../azure-monitor/agents/data-sources-performance-counters.md#configuring-performance-counters)
- 若要添加 Windows 事件，请参阅[配置 Windows 事件日志](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)

找不到用于帮助诊断问题的数据点？ 请向我们发送反馈！

- 若要了解如何留下反馈，请参阅 [Azure 虚拟桌面的故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 还可以在 [Azure 虚拟桌面反馈中心](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)提供关于 Azure 虚拟桌面的反馈。

## <a name="known-issues-and-limitations"></a>已知问题和限制

下面是我们已知并且正在努力修正的问题和限制：

- 一次只能监视一个主机池。 
- 若要保存最常用的设置，则必须保存工作簿的自定义模板。 自定义模板不会自动采用来自产品组的更新。
- 加载所选内容时，配置工作簿有时会显示“查询失败”错误。 请刷新该查询，视需要重新输入你的选择，错误应该会自行解决。 
- 有些错误消息的措辞方式对用户不太友好，并且文档仅提供了部分错误消息的说明。
- 总会话数性能计数器可以按小数字来过度计数会话，并且总会话数看起来可能会超出最大会话数限制。
- 可用会话计数不反映主机池上的缩放策略。   
- 你是否看到矛盾或意外的连接时间？ 在极少数情况下，连接的完成事件可能会丢失，这可能会影响某些视觉对象和指标。
- 用于连接的时间包括让用户输入其凭据所用的时间；这是与体验有关的，但在有些情况下可能会表现出假的高峰。 
    

## <a name="next-steps"></a>后续步骤

- 若要开始，请参阅[使用适用于 Azure 虚拟桌面的 Azure Monitor 监视部署](azure-monitor.md)。
- 若要估计、度量和管控数据存储成本，请参阅[估计 Azure Monitor 成本](azure-monitor-costs.md)。
- 查看[词汇表](azure-monitor-glossary.md)，详细了解与适用于 Azure 虚拟桌面的 Azure Monitor 相关的术语和概念。
