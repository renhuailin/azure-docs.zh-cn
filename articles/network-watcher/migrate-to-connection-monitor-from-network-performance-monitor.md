---
title: 从网络性能监视器迁移到连接监视器
titleSuffix: Azure Network Watcher
description: 了解如何从网络性能监视器迁移到连接监视器。
author: vinynigam
ms.service: network-watcher
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 84daa5527617584d420db02b012bec28760a146c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614555"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>从网络性能监视器迁移到连接监视器

> [!IMPORTANT]
> 自 2021 年 7 月 1 日起，你将无法在现有工作区中添加新测试，也无法通过网络性能监视器启用新的工作区。 可以继续使用在 2021 年 7 月 1 日之前创建的测试。 为了最大程度地减少当前工作负荷的服务中断，请在 2024 年 2 月 29 日之前，在 Azure 网络观察程序中将测试从网络性能监视器迁移到新连接监视器。

只需单击一下，就可以将测试从网络性能监视器 (NPM) 迁移到经过改进的新连接监视器，并且没有故障时间。 若要了解有关这些优势的详细信息，请参阅[连接监视器](./connection-monitor-overview.md)。


## <a name="key-points-to-note"></a>需要注意的要点

迁移将有助于生成以下结果：

* 本地代理和防火墙设置按原样工作。 不需要进行任何更改。 需要将安装在 Azure 虚拟机上的 Log Analytics 代理替换为[网络观察程序扩展](../virtual-machines/extensions/network-watcher-windows.md)。
* 现有测试已映射到“连接监视器”>“测试组”>“测试格式”。 通过选择“编辑”，可以查看和修改新连接监视器的属性，下载模板以对其进行更改，然后通过 Azure 资源管理器提交模板。
* 代理会将数据发送到 Log Analytics 工作区和指标。
* 数据监视：
   * **Log Analytics 中的数据**：迁移之前，数据将保留在 NetworkMonitoring 表中配置了 NPM 的工作区中。 迁移后，数据将移动到同一工作区中的 NetworkMonitoring 表、NWConnectionMonitorTestResult 表和 NWConnectionMonitorPathResult 表。 在 NPM 中禁用测试后，数据将仅存储在 NWConnectionMonitorTestResult 表和 NWConnectionMonitorPathResult 表中。
   * 基于日志的警报、仪表板和集成：必须基于新的 NWConnectionMonitorTestResult 表和 NWConnectionMonitorPathResult 表手动编辑查询。 若要在指标中重新创建警报，请参阅[使用连接监视器进行网络连接监视](./connection-monitor-overview.md#metrics-in-azure-monitor)。
* 对于 ExpressRoute 监视：
    * 端到端丢失和延迟：连接监视器支持此功能，并且它比 NPM 更简单，因为用户不需要配置要监视的线路和对等互连。 将自动发现路径中的线路，数据将在指标中提供（比 NPM 在其中存储结果的 LA 更快）。 拓扑也将按原样工作。
    * 带宽度量：通过启动与带宽相关的指标，NPM 基于日志分析的方法对 ExpressRoute 客户的带宽监视不起作用。 此功能目前在连接监视器中不可用。
    
## <a name="prerequisites"></a>先决条件

* 确保在订阅和 Log Analytics 工作区的区域中启用了网络观察程序。 如果未执行此操作，可能会出现一个错误，指出“在尝试迁移之前，请在选择订阅和所选的 LA 工作区位置中启用网络观察程序扩展”。
* 如果 Azure VM（所属的区域/订阅不同于 Log Analytics 工作区的区域/订阅）用作终结点，请确保已为订阅和区域启用网络观察程序。   
* 必须通过网络观察程序扩展启用安装有 Log Analytics 代理的 Azure 虚拟机。

## <a name="migrate-the-tests"></a>迁移测试

若要将测试从网络性能监视器迁移到连接监视器，请执行以下操作：

1. 在“网络观察程序”中，选择“连接监视器”，然后选择“从 NPM 导入测试”选项卡。 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="将测试从网络性能监视器迁移到连接监视器" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. 在下拉列表中，选择订阅和工作区，然后选择要迁移的 NPM 功能。 
1. 选择“导入”以迁移测试。
* 如果工作区中未启用 NPM，则会出现一个错误，指出“未找到有效的 NPM 配置”。 
* 如果在步骤 2 中选择的功能中不存在任何测试，则会出现一个错误，指出“所选工作区没有 \<feature\> 配置”。
* 如果没有有效的测试，则会出现一个错误，指出“所选工作区没有有效的测试”
* 你的测试可能包含不再处于活动状态，但可能在过去处于活动状态的代理。 会出现一个错误，指出“少数测试包含不再处于活动状态的代理。 非活动代理列表 - {0}。 这些代理可能在过去运行，但已关闭/不再运行。 启用代理并迁移到连接监视器。 单击‘继续’以迁移不包含非活动代理的测试”。

迁移开始后，将进行以下更改： 
* 创建新的连接监视器资源。
   * 每个区域和订阅都创建一个连接监视器。 对于使用本地代理的测试，新连接监视器名称的格式为 `<workspaceName>_"workspace_region_name"`。 对于使用 Azure 代理的测试，新连接监视器名称的格式为 `<workspaceName>_<Azure_region_name>`。
   * 监视数据现在存储在启用了 NPM 的同一个 Log Analytics 工作区中，在名为 NWConnectionMonitorTestResult 表和 NWConnectionMonitorPathResult 表的新表中。 
   * 测试名称将作为测试组名称继续使用。 不迁移测试说明。
   * 将创建源终结点和目标终结点，并在新测试组中使用它们。 对于本地代理，终结点的格式设置为 `<workspaceName>_<FQDN of on-premises machine>`。代理说明未迁移。
   * 目标端口和探测间隔移动到名为 `TC_<protocol>_<port>` 和 `TC_<protocol>_<port>_AppThresholds` 的测试配置。 协议基于端口值设置。 对于 ICMP，测试配置被命名为 `TC_<protocol>` 和 `TC_<protocol>_AppThresholds`。 如果设置了成功阈值和其他可选属性，则将其迁移，否则保留为空。
   * 如果迁移测试包含未运行的代理，则需要启用代理并再次迁移。
* 未禁用 NPM，所以迁移的测试可以继续发送数据到 NetworkMonitoring 表、NWConnectionMonitorTestResult 表和 NWConnectionMonitorPathResult 表。 此方法可确保现有基于日志的警报和集成不受影响。
* 新创建的连接监视器在连接监视器中可见。

迁移之后，请务必：
* 手动禁用 NPM 中的测试。 在执行此操作之前，你将继续为其付费。 
* 禁用 NPM 时，在 NWConnectionMonitorTestResult 表和 NWConnectionMonitorPathResult 表中重新创建警报或使用指标。 
* 将任何外部集成迁移到 NWConnectionMonitorTestResult 表和 NWConnectionMonitorPathResult 表。 外部集成的示例包括 Power BI 和 Grafana 中的仪表板，以及与安全信息和事件管理 (SIEM) 系统的集成。

## <a name="common-errors-encountered"></a>遇到常见错误

下面是迁移过程中遇到的一些常见错误： 

| 错误  |    Reason   |
|---|---|
| 找不到有效的 NPM 配置。 转到 NPM UI 以检查配置     |     当用户从 NPM 中选择“导入测试”以迁移测试，但工作区中未启用 NPM 时，则会出现此错误   |
|所选工作区没有“服务连接监视器”配置    |       当用户将测试从 NPM 的服务连接监视器迁移到连接监视器，但没有在服务连接监视器中配置测试时，将出现此错误 |
|所选工作区没有“ExpressRoute 监视器”配置    |     当用户将测试从 NPM 的 ExpressRoute 监视器迁移到连接监视器，但没有在 ExpressRoute 监视器中配置测试时，将出现此错误  |
|所选工作区没有“性能监视器”配置    |      当用户将测试从 NPM 的性能监视器迁移到连接监视器，但没有在性能监视器中配置测试时，将出现此错误 |
|所选工作区没有有效的“{0}”测试    |      当用户将测试从 NPM 迁移到连接监视器，但用户选择迁移的功能中没有有效测试时，将出现此错误  |
|在尝试迁移之前，请在选择订阅和所选的 LA 工作区位置中启用网络观察程序扩展      |      当用户将测试从 NPM 迁移到连接监视器，并且在所选的 LA 工作区中未启用网络观察程序扩展时，将出现此错误。 用户需要在迁移测试前启用 NW 扩展 |
|少数 {1} 测试包含不再处于活动状态的代理。 非活动代理列表 - {0}。 这些代理可能在过去运行，但已关闭/不再运行。 启用代理并迁移到连接监视器。 单击‘继续’以迁移不包含非活动代理的测试”       |    当用户将测试从 NPM 迁移到连接监视器，并且某些选定的测试包含非活动网络观察程序代理，或此类不再处于活动状态但在过去处于活动状态且已关闭的 NW 代理时，将出现此错误。 用户可以取消选择这些测试并继续选择迁移不包含任何此类非活动代理的测试  |
|{1} 测试包含不再处于活动状态的代理。 非活动代理列表 - {0}。 这些代理可能在过去运行，但已关闭/不再运行。 启用代理并迁移到连接监视器     | 当用户将测试从 NPM 迁移到连接监视器，并且选定的测试包含非活动网络观察程序代理，或此类不再处于活动状态但在过去处于活动状态且已关闭的 NW 代理时，将出现此错误。 用户需要启用代理，然后继续将这些测试迁移到连接监视器    |
|将测试导入连接监视器时出错     |    当用户尝试将测试从 NPM 迁移到 CM，但由于错误迁移未成功时，将出现此错误 |



## <a name="next-steps"></a>后续步骤

若要详细了解连接监视器，请参阅：
* [从连接监视器（经典版）迁移到连接监视器](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [使用 Azure 门户创建连接监视器](./connection-monitor-create-using-portal.md)
