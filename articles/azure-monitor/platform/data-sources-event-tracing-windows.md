---
title: 收集 Windows (ETW 事件跟踪) 事件以进行分析 Azure Monitor 日志
description: 了解如何在 Azure Monitor 日志中收集 Windows (ETW) 的事件跟踪以进行分析。
services: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: jamesfit
author: jimmyfit
ms.date: 01/29/2021
ms.openlocfilehash: 6239cf48794c74c5dd810fda42476df399300578
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223767"
---
# <a name="collecting-event-tracing-for-windows-etw-events-for-analysis-azure-monitor-logs"></a>收集 Windows (ETW 事件跟踪) 事件以进行分析 Azure Monitor 日志

*Windows (ETW) 的事件跟踪* 为用户模式应用程序和内核模式驱动程序提供了一种检测机制。 Log Analytics 代理用于收集写入到管理和操作[ETW 通道](https://docs.microsoft.com/windows/win32/wes/eventmanifestschema-channeltype-complextype)的[Windows 事件](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events)。 但是，有时需要捕获和分析其他事件，如写入分析通道的事件。  

## <a name="event-flow"></a>事件流

若要在 Azure Monitor 日志中成功收集 [基于清单的 ETW 事件](https://docs.microsoft.com/windows/win32/etw/about-event-tracing#types-of-providers) 以进行分析，必须使用适用于 WINDOWS (WAD) 的 [Azure 诊断扩展](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview) 。 在这种情况下，诊断扩展将充当 ETW 使用者，将事件写入 Azure 存储 (表) 为中间存储。 此处，它将存储在名为 **WADETWEventTable** 的表中。 然后 Log Analytics 收集 Azure 存储中的表数据，将其呈现为名为 **ETWEvent** 的表。

![事件流](./media/data-sources-event-tracing-windows/event-flow.png)

## <a name="configuring-etw-log-collection"></a>配置 ETW 日志收集

### <a name="step-1-locate-the-correct-etw-provider"></a>步骤1：查找正确的 ETW 提供程序

使用以下任一命令枚举源 Windows 系统上的 ETW 提供程序。

命令行：

```
logman query providers
```

PowerShell：
```
Get-NetEventProvider -ShowInstalled | Select-Object Name, Guid
```
还可以选择通过管道将此 PowerShell 输出传递给 Out-Gridview 来帮助导航。

记录 ETW 提供程序的名称和 GUID，它们与事件查看器中显示的分析日志或调试日志相一致，或者记录到要为其收集事件数据的模块。

### <a name="step-2-diagnostics-extension"></a>步骤2：诊断扩展

确保所有源系统上都 [安装](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-windows-install#install-with-azure-portal)了 *Windows 诊断扩展*。

### <a name="step-3-configure-etw-log-collection"></a>步骤3：配置 ETW 日志收集

1. 导航到虚拟机的 " **诊断设置** " 边栏选项卡

2. 选择 " **日志** " 选项卡

3. 向下滚动并启用 **Windows (ETW 事件跟踪) 事件** 选项 " ![ 诊断设置" 的屏幕截图](./media/data-sources-event-tracing-windows/enable-event-tracing-windows-collection.png)

4. 基于你要为其配置集合的提供程序设置提供程序 GUID 或提供程序类

5. 适当设置 [**日志级别**](https://docs.microsoft.com/windows/win32/etw/configuring-and-starting-an-event-tracing-session)

6. 单击提供的提供程序旁边的省略号，然后单击 "**配置**"

7. 确保将 **默认目标表** 设置为 **etweventtable**

8. 根据需要设置 [**关键字筛选器**](https://docs.microsoft.com/windows/win32/wes/defining-keywords-used-to-classify-types-of-events)

9. 保存提供程序和日志设置

生成匹配事件后，应该会看到 ETW 事件显示在 Azure 存储的 **WADetweventtable** 表中。 您可以使用 Azure 存储资源管理器来确认这一点。

### <a name="step-4-configure-log-analytics-storage-account-collection"></a>步骤4：配置 Log Analytics 存储帐户集合

按照 [以下说明](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-logs#collect-logs-from-azure-storage) 收集 Azure 存储中的日志。 配置后，ETW 事件数据应显示在 **ETWEvent** 表下的 Log Analytics 中。

## <a name="next-steps"></a>后续步骤
- 使用 [自定义字段](https://docs.microsoft.com/azure/azure-monitor/platform/custom-fields) 在 ETW 事件中创建结构
- 了解[日志查询](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)以便分析从数据源和解决方案中收集的数据。
