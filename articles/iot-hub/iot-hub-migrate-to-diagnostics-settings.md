---
title: 将 Azure IoT 中心操作监视迁移到 Azure Monitor 中的 IoT 中心资源日志 |Microsoft Docs
description: 如何将 Azure IoT 中心更新为使用 Azure Monitor 而不是操作监视来实时监视 IoT 中心上的操作状态。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: dfd819d82f5e35183802e33e5d423cad4de36c38
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461592"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-azure-monitor-resource-logs"></a>将 IoT 中心从操作监视迁移到 Azure Monitor 资源日志

使用 [操作监视](iot-hub-operations-monitoring.md) 功能跟踪 IoT 中心内操作状态的客户可将该工作流迁移到 [Azure Monitor 资源日志](../azure-monitor/platform/platform-logs-overview.md)，Azure Monitor 的一项功能。 资源日志提供了许多 Azure 服务的资源级诊断信息。

**IoT 中心的操作监视功能已弃用**，已从门户中删除。 本文提供了将工作负荷从操作监视移动到 Azure Monitor 资源日志的步骤。 若要详细了解弃用日程表，请参阅[利用 Azure Monitor 和 Azure 资源运行状况监视 Azure IoT 解决方案](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)。

## <a name="update-iot-hub"></a>更新 IoT 中心

若要在 Azure 门户中更新 IoT 中心，请首先创建一个诊断设置，然后关闭 "操作监视"。  

### <a name="create-a--diagnostic-setting"></a>创建诊断设置

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。

1. 在左侧窗格中的 " **监视**" 下，选择 " **诊断设置**"。 然后选择“添加诊断设置”。

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/open-diagnostic-settings.png" alt-text="突出显示“监视”部分的“诊断设置”的屏幕截图。":::

1. 在 " **诊断设置** " 窗格上，为诊断设置 "名称"。

1. 在 " **类别详细信息**" 下，选择要监视的操作的类别。 有关 IoT 中心可用的操作类别的详细信息，请参阅 [资源日志](monitor-iot-hub-reference.md#resource-logs)。

1. 在 " **目标详细信息**" 下，选择要将日志发送到的位置。 您可以选择这些目标的任意组合：

   * 存档到存储帐户
   * 流式传输到事件中心
   * 通过 Log Analytics 工作区发送到 Azure Monitor 日志

   以下屏幕截图显示了一个诊断设置，该设置将 "连接" 和 "设备遥测" 类别中的操作路由到 Log Analytics 工作区：

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/add-diagnostic-setting.png" alt-text="显示已完成的诊断设置的屏幕截图。":::

1. 选择“保存”，保存这些设置。

新设置在大约 10 分钟后生效。 之后，日志将出现在配置的目标中。 有关配置诊断的详细信息，请参阅[从 Azure 资源收集和使用日志数据](../azure-monitor/platform/platform-logs-overview.md)。

有关如何创建诊断设置的更多详细信息，包括 PowerShell 和 Azure CLI，请参阅 Azure Monitor 文档中的 [诊断设置](../azure-monitor/platform/diagnostic-settings.md) 。

### <a name="turn-off-operations-monitoring"></a>关闭操作监视

> [!NOTE]
> 从 2019 年 3 月 11 日开始，IoT 中心的 Azure 门户接口不再有操作监视功能。 以下步骤不再适用。 若要迁移，请确保将正确的类别路由到 Azure Monitor 以上诊断设置的目标。

在工作流中测试新的诊断设置后，可以关闭操作监视功能。 

1. 在 IoT 中心菜单中，选择“操作监视”。 

2. 在每个监视类别下，选择“无”。 

3. 保存操作监视更改。

## <a name="update-applications-that-use-operations-monitoring"></a>更新使用操作监视的应用程序

操作监视和资源日志的架构略有不同。 现在，必须更新使用操作监视的应用程序以映射到资源日志使用的架构，这一点很重要。

此外，IoT 中心资源日志为跟踪提供五种新类别。 更新应用程序的现有架构后，还要添加新类别：

* 云到设备孪生操作
* 设备到云孪生操作
* 孪生查询
* 作业操作
* 直接方法

有关特定架构结构，请参阅[资源日志](monitor-iot-hub-reference.md#resource-logs)。

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>以低延迟监视设备连接和断开连接事件

若要监视生产环境中的设备连接和断开连接事件，我们建议订阅事件网格上的[“设备已断开连接”  事件](iot-hub-event-grid.md#event-types)以获取警报并监视设备连接状态。 使用此[教程](iot-hub-how-to-order-connection-state-events.md)了解如何在 IoT 解决方案中集成 IoT 中心的设备已连接和设备已断开连接事件。

## <a name="next-steps"></a>后续步骤

[监视 IoT 中心](monitor-iot-hub.md)