---
title: 监视 Windows 虚拟桌面预览故障排除-Azure
description: 如何对 Windows 虚拟桌面 Azure Monitor 的问题进行故障排除。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1818dc558ba45e318b71e1443556cc48feaede8b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367667"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Windows 虚拟桌面 (预览的 Azure Monitor 疑难解答) 

>[!IMPORTANT]
>Windows 虚拟桌面 Azure Monitor 当前提供公共预览版。 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文提供了有关 Windows 虚拟桌面 (预览) Azure Monitor 常见问题的已知问题和解决方案。

## <a name="issues-with-configuration-and-setup"></a>配置和设置问题

如果配置工作簿无法正常运行，则可以使用以下资源手动设置你的环境：

- 若要手动启用诊断或访问 Log Analytics 工作区，请参阅 [将 Windows 虚拟桌面诊断发送到 Log Analytics](diagnostics-log-analytics.md)。
- 若要在主机上手动安装 Log Analytics 扩展，请参阅 [Log Analytics 适用于 Windows 的虚拟机扩展](../virtual-machines/extensions/oms-windows.md)。
- 若要设置新的 Log Analytics 工作区，请参阅 [在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。
- 若要添加或删除性能计数器，请参阅 [配置性能计数器](../azure-monitor/platform/data-sources-performance-counters.md)。
- 若要为 Log Analytics 工作区配置事件，请参阅 [使用 Log Analytics 代理收集 Windows 事件日志数据源](../azure-monitor/platform/data-sources-windows-events.md)。

## <a name="my-data-isnt-displaying-properly"></a>我的数据显示不正确

如果数据未正确显示，请检查配置、权限，并检查是否已取消阻止所需的 IP 地址。 

- 首先，请确保填写了配置工作簿中的所有字段，如 [使用 Windows 虚拟桌面 Azure Monitor 中所述来监视你的部署](azure-monitor.md)。 如果缺少任何计数器或事件，则与它们关联的数据不会出现在 Azure 门户中。

- 请检查你的访问权限 & 联系资源所有者以请求缺少的权限;监视 Windows 虚拟桌面的任何人都需要以下权限：

    - 对保存 Windows 虚拟桌面资源的 Azure 订阅进行读取访问
    - 对保存 Windows 虚拟桌面会话主机的订阅资源组的读取访问权限 
    - 对 Log Analytics 工作区的读访问权限

- 你可能需要在服务器的防火墙中打开传出端口，以允许 Azure Monitor 向门户发送数据，请参阅 [传出端口](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses)。 

- 看不到最近活动的数据？ 你可能需要等待15分钟，然后刷新该源。 Azure Monitor 在填充日志数据时的延迟时间为15分钟。 若要了解详细信息，请参阅 [Azure Monitor 中的日志数据引入时间](../azure-monitor/platform/data-ingestion-time.md)。

如果没有任何信息，但数据仍未正确显示，则可能是查询或数据源出现问题。 查看我们的已知问题和限制。 

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>我要为 Windows 虚拟桌面自定义 Azure Monitor

Windows 虚拟桌面 Azure Monitor 使用 Azure Monitor 工作簿。 使用工作簿可以保存 Windows 虚拟桌面工作簿模板的副本，并创建自己的自定义项。

按照设计，自定义工作簿模板不会自动采用 products 组中的更新。 有关详细信息，请参阅 [基于工作簿的见解疑难解答](../azure-monitor/insights/troubleshoot-workbooks.md) 和 [工作簿概述](../azure-monitor/platform/workbooks-overview.md)。

## <a name="i-cant-interpret-the-data"></a>我无法解释数据

在 [窗口虚拟桌面词汇表 Azure Monitor](azure-monitor-glossary.md)上了解有关数据术语的详细信息。

## <a name="the-data-i-need-isnt-available"></a>我所需的数据不可用

如果要监视更多性能计数器或事件，可以允许它们发送到 Log Analytics 工作区，并在 "主机诊断：主机浏览器" 中对其进行监视。 

- 若要添加性能计数器，请参阅 [配置性能计数器](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#configuring-performance-counters)
- 若要添加 Windows 事件，请参阅 [配置 Windows 事件日志](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events#configuring-windows-event-logs)

找不到可帮助诊断问题的数据点？ 向我们发送反馈！

- 若要了解如何留下反馈，请参阅 [Windows 虚拟桌面的故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 你还可以在 [Windows 虚拟桌面反馈中心](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) 或 [我们的 UserVoice 论坛](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)上为 windows 虚拟桌面提供反馈。

## <a name="known-issues-and-limitations"></a>已知问题和限制

这些是我们当前意识到的问题和限制：

- 一次只能监视一个主机池。 

- 若要保存收藏夹设置，必须保存工作簿的自定义模板。 自定义模板不会自动采用产品组中的更新。

- 有些错误消息不是以用户友好的方式组句方式的，文档中并未介绍所有错误消息。

- "总会话数" 性能计数器可以使用较小的数计数会话，并且总会话可能会超出最大会话限制。

- 可用会话计数不反映主机池上的缩放策略。 
    
- 虽然极少出现连接的完成事件，但这可能会影响某些视觉对象，例如随着时间的推移和用户的连接状态。  
    
- 配置工作簿仅支持在其资源组所在的同一区域内配置主机。 

- 连接时间包括用户输入其凭据所用的时间;这与体验相关，但在某些情况下可能会显示错误高峰。 
    

## <a name="next-steps"></a>后续步骤

如果不确定如何解释数据或者想要了解有关常见术语的详细信息，请查看 [Windows 虚拟桌面术语表的 Azure Monitor](azure-monitor-glossary.md)。 如果你想要了解如何为 Windows 虚拟桌面设置和使用 Azure Monitor，请参阅 [使用 Windows 虚拟桌面 Azure Monitor 来监视你的部署](azure-monitor.md)。