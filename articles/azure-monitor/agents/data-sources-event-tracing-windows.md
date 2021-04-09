---
title: 收集 Windows 事件跟踪 (ETW) 事件以在 Azure Monitor 日志中进行分析
description: 了解如何收集 Windows 事件跟踪 (ETW) 以在 Azure Monitor 日志中进行分析。
services: azure-monitor
ms.topic: conceptual
ms.author: jamesfit
author: jimmyfit
ms.date: 01/29/2021
ms.openlocfilehash: 73135d95a56dc03790b3b7368a276ebfc99275fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025061"
---
# <a name="collecting-event-tracing-for-windows-etw-events-for-analysis-azure-monitor-logs"></a>收集 Windows 事件跟踪 (ETW) 事件以在 Azure Monitor 日志中进行分析

Windows 事件跟踪 (ETW) 提供了一种用于检测用户模式应用程序和内核模式驱动程序的机制。 Log Analytics 代理用于收集写入到管理和操作 [ETW 通道](/windows/win32/wes/eventmanifestschema-channeltype-complextype)的 [Windows 事件](./data-sources-windows-events.md)。 但是，有时需要捕获和分析其他事件，例如写入分析通道的事件。  

## <a name="event-flow"></a>事件流

若要成功收集[基于清单的 ETW 事件](/windows/win32/etw/about-event-tracing#types-of-providers)以在 Azure Monitor 日志中进行分析，必须使用 Windows [Azure 诊断扩展](./diagnostics-extension-overview.md) (WAD)。 在这种情况下，诊断扩展充当 ETW 使用者，将事件作为中间存储写入 Azure 存储（表）。 此处，它将存储在名为 WADETWEventTable 的表中。 然后，Log Analytics 从 Azure 存储中收集表数据，并将其呈现为名为 ETWEvent 的表。

![事件流](./media/data-sources-event-tracing-windows/event-flow.png)

## <a name="configuring-etw-log-collection"></a>配置 ETW 日志收集

### <a name="step-1-locate-the-correct-etw-provider"></a>步骤 1：查找正确的 ETW 提供程序

使用以下任一命令枚举源 Windows 系统上的 ETW 提供程序。

命令行：

```
logman query providers
```

PowerShell：
```
Get-NetEventProvider -ShowInstalled | Select-Object Name, Guid
```
（可选）可以选择将此 PowerShell 输出通过管道传输到 Out-Gridview 以帮助导航。

记录与“事件查看器”中显示的“分析”或“调试”日志或与要为其收集事件数据的模块一致的 ETW 提供程序名称和 GUID。

### <a name="step-2-diagnostics-extension"></a>步骤 2：诊断扩展

确保在所有源系统上都[安装](./diagnostics-extension-windows-install.md#install-with-azure-portal)了 Windows 诊断扩展。

### <a name="step-3-configure-etw-log-collection"></a>步骤 3：配置 ETW 日志收集

1. 导航到虚拟机的“诊断设置”边栏选项卡

2. 选择“日志”选项卡

3. 向下滚动并启用“Windows 事件跟踪 (ETW) 事件”选项“![诊断设置的屏幕截图](./media/data-sources-event-tracing-windows/enable-event-tracing-windows-collection.png)”

4. 根据你要为其配置集合的提供程序来设置提供程序 GUID 或提供程序类

5. 适当设置[日志级别](/windows/win32/etw/configuring-and-starting-an-event-tracing-session)

6. 单击提供的提供程序旁边的省略号，然后单击“配置”

7. 确保将“默认目标表”设置为“etweventtable” 

8. 根据需要设置[关键字筛选器](/windows/win32/wes/defining-keywords-used-to-classify-types-of-events)

9. 保存提供程序和日志设置

生成匹配事件后，应会看到 Azure 存储的 WADetweventtable 表中显示 ETW 事件。 可以使用 Azure 存储资源管理器来确认这一点。

### <a name="step-4-configure-log-analytics-storage-account-collection"></a>步骤 4：配置 Log Analytics 存储帐户集合

遵循[这些说明](./diagnostics-extension-logs.md#collect-logs-from-azure-storage)，从 Azure 存储收集日志。 配置完成后，ETWEvent 表下的 Log Analytics 中应会出现 ETW 事件数据。

## <a name="next-steps"></a>后续步骤
- 使用[自定义字段](../logs/custom-fields.md)在 ETW 事件中创建结构
- 了解[日志查询](../logs/log-query-overview.md)以便分析从数据源和解决方案中收集的数据。