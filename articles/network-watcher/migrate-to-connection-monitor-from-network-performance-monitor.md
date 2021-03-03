---
title: 从网络性能监视器迁移到连接监视器
titleSuffix: Azure Network Watcher
description: 了解如何从网络性能监视器迁移到连接监视器。
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: e5053284de18740b761df3e5df256cc79d2e8f1c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698682"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>从网络性能监视器迁移到连接监视器

> [!IMPORTANT]
> 自2021年7月1日起，你将无法在现有工作区中添加新的测试，也无法启用网络性能监视器的新工作区。 你可以继续使用在2021年7月之前创建的测试。 若要最大程度地减少对当前工作负荷的服务中断，请在2024年2月29日之前将测试从网络性能监视器迁移到 Azure 网络观察程序中的新连接监视器。

只需单击一下，就可以将测试从网络性能监视器 (NPM) 迁移到经过改进的新连接监视器，并且没有故障时间。 若要了解有关这些优势的详细信息，请参阅[连接监视器](./connection-monitor-overview.md)。


## <a name="key-points-to-note"></a>需要注意的要点

迁移将有助于生成以下结果：

* 本地代理和防火墙设置按原样工作。 不需要进行任何更改。 需要将安装在 Azure 虚拟机上的 Log Analytics 代理替换为 [网络观察程序扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)。
* 现有测试已映射到“连接监视器”>“测试组”>“测试格式”。 通过选择“编辑”，可以查看和修改新连接监视器的属性，下载模板以对其进行更改，然后通过 Azure 资源管理器提交模板。
* 代理会将数据发送到 Log Analytics 工作区和指标。
* 数据监视：
   * **Log Analytics 中的数据**：迁移之前，数据将保留在 NetworkMonitoring 表中配置了 NPM 的工作区中。 迁移后，数据将转到同一工作区中的 NetworkMonitoring 表、NWConnectionMonitorTestResult 表和 NWConnectionMonitorPathResult 表。 在 NPM 中禁用测试后，数据仅存储在 NWConnectionMonitorTestResult 表和 NWConnectionMonitorPathResult 表中。
   * **基于日志的警报、仪表板和集成**：你必须基于新的 NWConnectionMonitorTestResult 表和 NWConnectionMonitorPathResult 表手动编辑查询。 若要在指标中重新创建警报，请参阅[使用连接监视器进行网络连接监视](./connection-monitor-overview.md#metrics-in-azure-monitor)。
    
## <a name="prerequisites"></a>先决条件

* 确保在订阅和 Log Analytics 工作区的区域中启用了网络观察程序。 
* 如果 Azure VM 使用的是与 Log Analytics 工作区不同的区域/订阅，请确保为该订阅和区域启用网络观察程序。   
* 必须通过网络观察程序扩展启用安装有 Log Analytics 代理的 Azure 虚拟机。

## <a name="migrate-the-tests"></a>迁移测试

若要将测试从网络性能监视器迁移到连接监视器，请执行以下操作：

1. 在网络观察程序中，选择“连接监视器”，然后选择“从 NPM 迁移测试”选项卡 。 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="将测试从网络性能监视器迁移到连接监视器" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. 在下拉列表中，选择订阅和工作区，然后选择要迁移的 NPM 功能。 
1. 选择“导入”以迁移测试。

迁移开始后，将进行以下更改： 
* 创建新的连接监视器资源。
   * 每个区域和订阅都创建一个连接监视器。 对于使用本地代理的测试，新连接监视器名称的格式为 `<workspaceName>_"workspace_region_name"`。 对于使用 Azure 代理的测试，新连接监视器名称的格式为 `<workspaceName>_<Azure_region_name>`。
   * 监视数据现在存储在启用了 NPM 的同一 Log Analytics 工作区中，在名为 NWConnectionMonitorTestResult table 和 NWConnectionMonitorPathResult 表的新表中。 
   * 测试名称将作为测试组名称继续使用。 不迁移测试说明。
   * 将创建源终结点和目标终结点，并在新测试组中使用它们。 对于本地代理，终结点的格式为 `<workspaceName>_<FQDN of on-premises machine>`。
   * 目标端口和探测间隔将移到名为和的测试配置 `TC_<protocol>_<port>` `TC_<protocol>_<port>_AppThresholds` 。 协议基于端口值设置。 对于 ICMP，测试配置被命名为 `TC_<protocol>` 和 `TC_<protocol>_AppThresholds` 。 如果迁移设置，则为成功阈值和其他可选属性，否则保留为空。
   * 如果迁移测试包含未运行的代理，则需要启用代理并再次迁移。
* NPM 未被禁用，因此迁移的测试可以继续将数据发送到 NetworkMonitoring 表 NWConnectionMonitorTestResult 表和 NWConnectionMonitorPathResult 表。 此方法可确保现有基于日志的警报和集成不受影响。
* 新创建的连接监视器在连接监视器中可见。

迁移之后，请务必：
* 手动禁用 NPM 中的测试。 在执行此操作之前，你将继续为其付费。 
* 禁用 NPM 时，请在 NWConnectionMonitorTestResult 和 NWConnectionMonitorPathResult 表中重新创建警报或使用度量值。 
* 将任何外部集成迁移到 NWConnectionMonitorTestResult 表和 NWConnectionMonitorPathResult 表。 外部集成的示例包括 Power BI 和 Grafana 中的仪表板，以及与安全信息和事件管理 (SIEM) 系统的集成。


## <a name="next-steps"></a>后续步骤

若要详细了解连接监视器，请参阅：
* [从连接监视器（经典版）迁移到连接监视器](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [使用 Azure 门户创建连接监视器](./connection-monitor-create-using-portal.md)
