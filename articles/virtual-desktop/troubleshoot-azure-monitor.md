---
title: 监视 Windows 虚拟桌面预览故障排除-Azure
description: 如何对 Windows 虚拟桌面 Azure Monitor 的问题进行故障排除。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91cf6729911cdb674c5451f172e76a2e9d5943e4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466337"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Windows 虚拟桌面 (预览的 Azure Monitor 疑难解答) 

>[!IMPORTANT]
>Windows 虚拟桌面 Azure Monitor 当前提供公共预览版。 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文提供了有关 Windows 虚拟桌面 (预览) Azure Monitor 常见问题的已知问题和解决方案。

## <a name="the-configuration-workbook-isnt-working-properly"></a>配置工作簿工作不正常

如果 Azure Monitor 配置工作簿不起作用，则可以使用这些资源手动设置其部分：

- 若要手动启用诊断或访问 Log Analytics 工作区，请参阅 [将 Windows 虚拟桌面诊断发送到 Log Analytics](diagnostics-log-analytics.md)。
- 若要在主机上手动安装 Log Analytics 扩展，请参阅 [Log Analytics 适用于 Windows 的虚拟机扩展](../virtual-machines/extensions/oms-windows.md)。
- 若要设置新的 Log Analytics 工作区，请参阅 [在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。
- 若要添加或删除性能计数器，请参阅 [配置性能计数器](../azure-monitor/platform/data-sources-performance-counters.md)。
- 若要为 Log Analytics 工作区配置事件，请参阅 [使用 Log Analytics 代理收集 Windows 事件日志数据源](../azure-monitor/platform/data-sources-windows-events.md)。

或者，此问题可能是由于缺少资源或没有所需的权限引起的。

如果订阅没有任何 Windows 虚拟桌面资源，则它不会显示在 *订阅* 参数中。

如果没有对正确订阅的读取访问权限，它们不会显示在 *订阅* 参数中，并且不会在仪表板中看到其数据。 若要解决此问题，请与你的订阅所有者联系并请求读取访问权限。

## <a name="my-data-isnt-displaying-properly"></a>我的数据显示不正确

如果数据未正确显示，则在 Azure Monitor 配置过程中可能发生了某些情况。 首先，请确保填写了配置工作簿中的所有字段，如 [使用 Windows 虚拟桌面 Azure Monitor 中所述来监视你的部署](azure-monitor.md)。 你可以随时更改新的和现有环境的设置。 如果缺少任何计数器或事件，则与它们关联的数据不会出现在 Azure 门户中。

如果没有任何信息，但数据仍未正确显示，则可能是查询或数据源出现问题。 

如果看不到任何安装错误，仍看不到预期的数据，则可能需要等待15分钟，然后刷新源。 Azure Monitor 在填充日志数据时的延迟时间为15分钟。 若要了解详细信息，请参阅 [Azure Monitor 中的日志数据引入时间](../azure-monitor/platform/data-ingestion-time.md)。

最后，如果不缺少任何信息，但数据仍未显示，则可能是查询或数据源出现问题。 如果是这种情况，你可能需要联系支持部门以解决问题。

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>我要为 Windows 虚拟桌面自定义 Azure Monitor

Windows 虚拟桌面 Azure Monitor 使用 Azure Monitor 工作簿。 使用工作簿可以保存 Windows 虚拟桌面工作簿模板的副本，并创建自己的自定义项。

当产品组更新原始模板时，自定义模板不会更新。 这是在 "工作簿" 工具中设计的，你将需要保存更新模板的副本，然后重新生成自定义以采用更新。 有关详细信息，请参阅 [基于工作簿的见解疑难解答](../azure-monitor/insights/troubleshoot-workbooks.md) 和 [工作簿概述](../azure-monitor/platform/workbooks-overview.md)。

## <a name="i-cant-interpret-the-data"></a>我无法解释数据

在 [窗口虚拟桌面词汇表 Azure Monitor](azure-monitor-glossary.md)上了解有关数据术语的详细信息。

## <a name="the-data-i-need-isnt-available"></a>我所需的数据不可用

找不到可帮助诊断问题的数据点？ 向我们发送反馈！

- 若要了解如何留下反馈，请参阅 [Windows 虚拟桌面的故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 你还可以在 [Windows 虚拟桌面反馈中心](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) 或 [我们的 UserVoice 论坛](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)上为 windows 虚拟桌面提供反馈。

## <a name="known-issues"></a>已知问题

这些是我们当前了解和解决问题的问题：

- 目前，一次只能选择一个订阅、资源组和主机池。 因此，当使用 "用户报表" 页来了解用户的体验时，需要验证您是否具有用户使用的正确主机池，或者其数据将不填充视觉对象。

- 目前不能将收藏夹设置保存到 Azure Monitor，除非保存工作簿的自定义模板。 这意味着，在每次打开 Windows 虚拟桌面 Azure Monitor 时，IT 管理员都必须输入他们的订阅名称、资源组名称和主机池首选项。

- 目前无法将 Windows 虚拟桌面 Azure Monitor 的数据导出到 Excel 中。

- 对于所选订阅中的所有产品，所有严重性为 1 Azure Monitor 警报都将显示在 "概述" 页中。 这是由设计决定的，因为订阅中其他产品的警报可能会影响 Windows 虚拟桌面。 现在，查询仅限于严重性为1的警报，不包括 "概述" 页中的高优先级严重性0警报。

- 有些错误消息不是以用户友好的方式组句方式的，文档中并未介绍所有错误消息。

## <a name="next-steps"></a>后续步骤

如果不确定如何解释数据或者想要了解有关常见术语的详细信息，请查看 [Windows 虚拟桌面术语表的 Azure Monitor](azure-monitor-glossary.md)。 如果你想要了解如何为 Windows 虚拟桌面设置和使用 Azure Monitor，请参阅 [使用 Windows 虚拟桌面 Azure Monitor 来监视你的部署](azure-monitor.md)。