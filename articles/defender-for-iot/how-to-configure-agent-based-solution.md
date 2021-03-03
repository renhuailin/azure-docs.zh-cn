---
title: 配置 Azure Defender for IoT 基于代理的解决方案
description: 了解如何在 Azure Defender 中配置用于 IoT 代理的解决方案的数据收集
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: e32b9c690db70baeda7dec2f9f1270f82c6881bd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733289"
---
# <a name="configure-azure-defender-for-iot-agent-based-solution"></a>配置 Azure Defender for IoT 基于代理的解决方案  

本文介绍如何在 Azure Defender 中配置基于 IoT 代理的解决方案的数据收集。

## <a name="configure-data-collection"></a>配置数据收集

在 Azure Defender 中配置用于基于 IoT 代理的解决方案的数据收集： 

1. 导航到 "Azure 门户"，并选择已附加到 IoT 的 IoT 中心 

1. 在 " **安全**"   菜单下，选择 "**设置**"。 

1. 选择 " **数据收集**"。 

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-collection.png" alt-text="从 &quot;安全&quot; 菜单设置中选择 &quot;数据收集&quot;。":::

## <a name="geolocation-and-ip-address-handling"></a>地理位置和 IP 地址处理 

为了保护 IoT 解决方案的安全，默认情况下会收集和存储 IoT 设备、IoT Edge 和 IoT)  (中心的传入和传出连接的 IP 地址。 此信息非常重要，用于检测可疑 IP 地址源的异常连接。 例如，当尝试尝试建立来自已知僵尸网络的 IP 地址源的连接时，或从地理位置之外的 IP 地址源建立连接时。 使用 IoT service，可以随时灵活地启用和禁用 IP 地址数据收集。 

若要启用或禁用 IP 地址数据收集，请执行以下操作： 

1. 打开 IoT 中心，然后 ****   从 " **安全**" 菜单中选择 "设置"   。 

1. 选择 " **数据收集**"   屏幕，根据需要修改地理位置和 IP 地址处理设置。 

## <a name="log-analytics-creation"></a>创建 Log Analytics 

Defender for IoT 允许在 Log Analytics 工作区中存储安全警报、建议和原始安全数据。 默认情况下，IoT 中心中的 Log Analytics 引入在 IoT 解决方案中设置为 **off** 。 可以将 IoT 附加到日志分析工作区，也可以将安全数据存储在此处。 

默认情况下，在 Log Analytics 工作区中，通过 Defender 对 IoT 存储两种类型的信息：
 
- 安全警报。

- 针对. 

您可以选择添加其他信息类型的存储 `raw events` 。 

> [!Note] 
>  `raw events`   在 Log Analytics 中存储会带来额外的存储成本。 

启用 Log Analytics 以使用微代理： 

1. 导航到 "**工作区配置**  >  **数据收集**"，并切换到 " **打开**"。 

1. 创建新的 Log Analytics 工作区或附加现有的工作区。 

1. 验证是否选择了 " **访问原始安全数据**"   选项。  

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-settings.png" alt-text="确保选中 &quot;对原始安全数据的访问&quot;。":::

1. 选择“保存”。 ****

每个月，每个客户向 Azure Log Analytics 服务引入的前 5 gb 数据是免费的。 每 gb 数据引入到你的 Azure Log Analytics 工作区中，前31天不收取任何费用。 有关定价的详细信息，请参阅 [Log Analytics 定价](https://azure.microsoft.com/pricing/details/monitor/)。 

若要更改 Log Analytics 的工作区配置，请执行以下操作： 

1. 在 IoT 中心的 " **安全** " 菜单中，选择 " **设置**"。 

1. 选择 " **数据收集**"   屏幕，根据需要修改 Log Analytics 设置的工作区配置。 

若要在配置后访问 Log Analytics 工作区中的警报：

1. 选择用于 IoT 的警报。

1. **在 Log Analytics 工作区中选择调查警报**。

若要在配置后访问 Log Analytics 工作区中的警报：

1. 选择用于 IoT 的 "建议"。

1. **在 Log Analytics 工作区中选择调查建议**。 
 
有关从 Log Analytics 查询数据的详细信息，请参阅 [Log Analytics 中的查询入门](../azure-monitor/logs/get-started-queries.md)。 

## <a name="turn-off-defender-for-iot"></a>关闭 Defender for IoT 

若要打开或关闭特定 IoT 中心上的 IoT 服务 Defender： 

1. 在 IoT 中心的 " **安全** " 菜单中，选择 " **设置**"。

1. 选择 " **数据收集**"   屏幕，根据需要修改 Log Analytics 设置的工作区配置。

## <a name="next-steps"></a>后续步骤 

转到下一篇文章， [配置解决方案](quickstart-configure-your-solution.md)。