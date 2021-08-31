---
title: 从连接监视器迁移到连接监视器
titleSuffix: Azure Network Watcher
description: 了解如何从连接监视器迁移到连接监视器。
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
ms.openlocfilehash: c9130b83d0d4491152e05157c9cb52dfb89231a1
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113105734"
---
# <a name="migrate-to-connection-monitor-from-connection-monitor-classic"></a>从连接监视器（经典版）迁移到连接监视器

> [!IMPORTANT]
> 自 2021 年 7 月 1 日起，你将无法在连接监视器（经典版）中添加新的连接监视器，但可以继续使用在 2021 年 7 月 1 日之前创建的现有连接监视器。 为了最大程度地减少当前工作负载的服务中断，请于 2024 年 2 月 29 日之前，在 Azure 网络观察程序中[从连接监视器（经典版）迁移到新连接监视器](migrate-to-connection-monitor-from-connection-monitor-classic.md)。

只需点击几下鼠标，就能将现有连接监视器迁移到经过改进的新连接监视器，并且无需停机。 要了解有关这些优势的详细信息，请参阅[连接监视器](./connection-monitor-overview.md)。

## <a name="key-points-to-note"></a>需要注意的要点

迁移将有助于生成以下结果：

* 代理和防火墙设置按原样工作。 不需要进行任何更改。 
* 现有的连接监视器已映射到“连接监视器”>“测试组”>“测试格式”。 通过选择“编辑”，可以查看和修改新连接监视器的属性，下载模板以对连接监视器进行更改，然后通过 Azure 资源管理器提交。 
* 具有网络观察程序扩展的 Azure 虚拟机将数据发送到工作区和指标。 连接监视器通过新指标（ChecksFailedPercent 和 RoundTripTimeMs）而不是旧指标（ProbesFailedPercent 和 AverageRoundtripMs）提供数据。 旧指标将迁移到新指标，如 > ProbesFailedPercent -> ChecksFailedPercent 和 AverageRoundtripMs -> RoundTripTimeMs。
* 数据监视：
   * **警报**：自动迁移到新指标。
   * **仪表板和集成**：需要手动编辑指标集。 
    
## <a name="prerequisites"></a>先决条件

1. 如果使用自定义工作区，请确保在订阅中和 Log Analytics 工作区的区域中启用了网络观察程序。 否则，可能会出现一个错误，指出“在尝试迁移之前，请在选择订阅和所选的 LA 工作区位置中启用网络观察程序扩展”。
1. 如果在连接监视器（经典）中用作源的虚拟机不再启用网络观察程序扩展，将出现一条错误消息，指出“无法导入具有以下测试的连接监视器，因为一个或多个 Azure 虚拟机未安装网络观察程序扩展。 安装网络观察程序扩展，并单击‘刷新’以导入它们。”



## <a name="migrate-the-connection-monitors"></a>迁移连接监视器

1. 要将旧连接监视器迁移到新连接监视器，请选择“连接监视器”，然后选择“迁移连接监视器” 。

    ![屏幕截图显示了如何从连接监视器迁移到连接监视器。](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. 选择你的订阅和要迁移的连接监视器，然后选择“迁移所选内容”。 

只需点击几下，就可以将现有的连接监视器迁移到连接监视器。 一旦从 CM（经典）迁移到 CM，你将无法查看 CM（经典）下的监视器

现在，可以自定义连接监视器属性、更改默认工作区、下载模板并检查迁移状态。 

迁移开始后，将进行以下更改： 
* Azure 资源管理器资源会更改为新的连接监视器。
    * 连接监视器的名称、区域和订阅保持不变。 资源 ID 不受影响。
    * 除非自定义连接监视器，否则将在订阅和连接监视器的区域中创建默认 Log Analytics 工作区。 此工作区是监视数据的存储位置。 测试结果数据也存储在指标中。
    * 每个测试都将迁移到名为 defaultTestGroup 的测试组。
    * 新测试组中创建并使用了源终结点和目标终结点。 默认名称是 defaultSourceEndpoint 和 defaultDestinationEndpoint 。
    * 目标端口和探测间隔已移至名为 defaultTestConfiguration 的测试配置。 协议基于端口值设置。 成功阈值和其他可选属性将留空。
* 指标警报已迁移到连接监视器指标警报。 指标不同，因此有所变化。 有关详细信息，请参阅[使用连接监视器进行网络连接监视](./connection-monitor-overview.md#metrics-in-azure-monitor)。
* 已迁移的连接监视器不再显示为旧的连接监视器解决方案。 它们现在仅可用于连接监视器。
* 必须手动迁移所有外部集成，例如 Power BI 和 Grafana 中的面板，以及与安全信息和事件管理 (SIEM) 系统的集成。 这是迁移安装程序所需执行的唯一手动步骤。

## <a name="common-errors-encountered"></a>遇到常见错误

下面是迁移过程中遇到的一些常见错误： 

| 错误  |    Reason   |
|---|---|
|无法导入以下连接监视器，因为一个或多个订阅/区域组合未启用网络观察程序。 启用网络观察程序，并单击“刷新”以导入它们。 连接监视器列表 - {0}   |  当用户将测试从 CM（经典）迁移到连接监视器，而未在 CM（经典）的一个或多个订阅和区域中启用网络观察程序扩展时，则会出现此错误。 用户需要在订阅中启用 NW 扩展并刷新以导入它们，然后再重新迁移   |
|无法导入具有以下测试的连接监视器，因为一个或多个 Azure 虚拟机未安装网络观察程序扩展。 安装网络观察程序扩展，并单击“刷新”以导入它们。 测试列表 - {0} |    当用户将测试从 CM（经典）迁移到连接监视器，而未在 CM（经典）的一个或多个 Azure VM 中安装网络观察程序扩展时，则会出现此错误。 用户需要在 Azure VM 中安装 NW 扩展并刷新，然后再重新迁移 |
|没有行可供显示   |  当用户尝试将订阅从 CM（经典）迁移到 CM，但在订阅中未创建 CM（经典）时，将出现此错误 |

## <a name="next-steps"></a>后续步骤

若要详细了解连接监视器，请参阅：
* [从网络性能监视器迁移到连接监视器](./migrate-to-connection-monitor-from-network-performance-monitor.md)
* [使用 Azure 门户创建连接监视器](./connection-monitor-create-using-portal.md)


    
 
    
