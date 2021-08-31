---
title: 配置 Azure Defender for IoT 基于代理的解决方案
description: 了解如何在 Azure Defender for IoT 基于代理的解决方案中配置数据收集
ms.date: 05/26/2021
ms.topic: how-to
ms.openlocfilehash: 94f8548895da57efe450342a25bd5de086af0998
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014519"
---
# <a name="configure-azure-defender-for-iot-agent-based-solution"></a>配置 Azure Defender for IoT 基于代理的解决方案  

本文介绍如何在 Azure Defender for IoT 基于代理的解决方案中配置数据收集。

## <a name="configure-data-collection"></a>配置数据收集

若要在 Azure Defender for IoT 基于代理的解决方案中配置数据收集，请执行以下操作： 

1. 导航到 Azure 门户，并选择 Defender for IoT 附加到的 IoT 中心 

1. 在“安全” 菜单下，选择“设置” **** 。 

1. 选择“数据收集” **** 。 

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-collection.png" alt-text="从安全菜单设置中选择数据收集。":::

## <a name="geolocation-and-ip-address-handling"></a>地理位置和 IP 地址处理 

为了保护 IoT 解决方案，系统在默认情况下会收集并存储 IoT 设备、IoT Edge 和 IoT 中心的传入和传出连接的 IP 地址。 这些信息是必不可少的，并且会用于检测来自可疑 IP 源的异常连接。 例如，当存在从某个已知僵尸网络的 IP 地址源或从你的地理位置之外的某个 IP 地址源建立连接的尝试时。 利用 Defender for IoT 服务，可以灵活地随时启用和禁用对 IP 地址数据的收集。 

若要启用或禁用收集 IP 地址数据，请执行以下操作： 

1. 打开 IoT 中心，然后从“安全”菜单中选择“设置” ****   ****  。 

1. 选择“数据收集”屏幕，根据需要修改地理位置和 IP 地址处理设置 ****  。 

## <a name="log-analytics-creation"></a>创建 Log Analytics 

使用 Defender for IoT，可以在 Log Analytics 工作区中存储安全警报、建议和原始安全数据。 在默认情况下，IoT 中心的 Log Analytics 引入在 Defender for IoT 解决方案中设置为“关闭”状态。 可以将 Defender for IoT 附加到 Log Analytics 工作区，也可以将安全数据存储在该工作区。 

在默认情况下，Defender for IoT 在 Log Analytics 工作区中存储两类信息：
 
- 安全警报。

- 建议。 

可以选择添加作为 `raw events` 的附加信息类型的存储。 

> [!Note] 
> 在 Log Analytics 中存储  `raw events`  会产生额外的存储成本。 

若要启用 Log Analytics 以用于微代理，请执行以下操作： 

1. 导航到“工作区配置” > “数据收集”，并将开关切换到“打开”  **** 。 

1. 创建新的（或附加一个现有的）Log Analytics 工作区。 

1. 验证是否选择了“访问原始安全数据”选项 ****  。  

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-settings.png" alt-text="确保选择了“访问原始安全数据”。":::

1. 选择“保存”。 ****

每个客户每个月引入 Azure Log Analytics 服务的前 5 GB 数据是免费的。 引入 Azure Log Analytics 工作区的每 GB 数据会免费保留 31 天。 有关定价的详细信息，请参阅 [Log Analytics 定价](https://azure.microsoft.com/pricing/details/monitor/)。 

若要更改 Log Analytics 的工作区配置，请执行以下操作： 

1. 在 IoT 中心的“安全”菜单中，选择“设置” **** 。 

1. 选择“数据收集”屏幕，根据需要修改 Log Analytics 设置的工作区配置 ****  。 

若要在配置之后访问 Log Analytics 工作区中的警报，请执行以下操作：

1. 在 Defender for IoT 中选择警报。

1. 选择“调查 Log Analytics 工作区中的警报”。

若要在配置之后访问 Log Analytics 工作区中的警报，请执行以下操作：

1. 选择 Defender for IoT 中的建议。

1. 选择“调查 Log Analytics 工作区中的建议”。 
 
若要详细了解如何从 Log Analytics 查询数据，请参阅  [Azure Monitor 中的日志查询入门](../../azure-monitor/logs/get-started-queries.md)。 

## <a name="turn-off-defender-for-iot"></a>关闭 Defender for IoT 

若要针对某个特定 IoT 中心启用或禁用 Defender for IoT 服务，请执行以下操作： 

1. 在 IoT 中心的“安全”菜单中，选择“设置” **** 。

1. 选择“数据收集”屏幕，根据需要修改 Log Analytics 设置的工作区配置 ****  。

## <a name="next-steps"></a>后续步骤 

转到下一篇文章，以了解如何[配置解决方案](quickstart-configure-your-solution.md)。