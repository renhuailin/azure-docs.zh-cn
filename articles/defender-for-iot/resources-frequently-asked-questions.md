---
title: Defender for IoT 常见问题解答
description: 查找有关 Azure Defender for IoT 功能和服务的最常见问题的解答。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.openlocfilehash: 678b50ffb2df2cd16312548109b93f6f1a7f08ff
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247459"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Azure Defender for IoT 常见问题解答

本文提供了有关 Defender for IoT 的常见问题和解答的列表。

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>什么是 Azure 独特的 IoT 安全性价值主张？

Defender for IoT 使企业能够将其现有网络安全视图扩展到整个 IoT 解决方案。 Azure 提供了业务解决方案的端到端视图，使你能够根据企业安全状况和收集的数据来进行业务相关操作和决策。 使用 Azure IoT、Azure IoT Edge 和 Azure 安全中心的组合安全性使你可以创建具有所需安全性的所需解决方案。

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>我们组织使用专有的非标准工业协议。 是否支持它们？ 

Azure Defender for IoT 提供全面的协议支持。 除嵌入式协议支持外，还可以保护运行专有和自定义协议或非标准协议的 IoT 和设备。 使用 Horizon 开放式开发环境 (ODE) SDK，开发人员可以创建析像器插件，用于基于定义的协议解码网络流量。 流量由服务进行分析，以提供完整的监视、警报和报告功能。 使用 Horizon 可以：
- 无需升级到新版本即可扩展可见性和控件。
- 通过现场开发以外部插件的形式来保护专有信息。 
- 将警报、事件和协议参数的文本本地化。

这是将协议作为插件进行开发的唯一解决方案，不需要专门的开发人员团队或版本来支持新的协议。 开发人员、合作伙伴和客户可以使用 Horizon 安全地开发协议，并分享见解和知识。 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>我必须从 Microsoft 合作伙伴那里购买硬件设备吗？
Azure Defender for IoT 传感器根据[硬件规范指南](./how-to-identify-required-appliances.md)中所述的特定硬件规格运行，客户可以从 Microsoft 合作伙伴处购买经过认证的硬件，也可以使用提供的材料清单 (BOM) 自行购买。 

经过认证的硬件已在实验室中测试了驱动程序稳定性、丢包情况和网络规模。


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>根据规定，我们禁止将系统连接到 Internet。 我们是否仍可使用 Defender for IoT？

可以！ Azure Defender for IoT 平台本地解决方案部署为物理或虚拟传感器设备，该设备会被动地（通过 SPAN、RSPAN 或 TAP）引入网络流量来分析、发现和持续监视 IT、OT 和 IoT 网络。 在大型企业中，多个传感器可以将其数据聚合到本地管理控制台。

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>监视端口应连接到网络中的哪个位置？

Azure Defender for IoT 传感器连接到 SPAN 端口或网络 TAP ，并立即开始通过被动（无代理）监视来收集 ICS 网络流量。 它不会对 OT 网络造成任何影响，因为它不会放置在数据路径中，也不会主动扫描 OT 设备。

例如：
- 单个设备（虚拟设备或物理设备）可以在车间 DMZ 层中，将所有车间单元的流量路由到此层。
- 另外，还可以通过车间 DMZ 层中的云或本地管理找到每个车间单元的微型传感器。 其他设备（虚拟设备或物理设备）可以监视车间 DMZ 层中的流量（适用于 SCADA、Historian 或 MES）。

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Defender for IoT 与竞争对手相比如何？

其他解决方案提供了一组允许客户创建自己的解决方案的功能，而 Defender for IoT 提供了独特的端到端 IoT 安全性解决方案，可全面查看所有相关 Azure 资源的安全性。 Azure 可实现快速部署并与 IoT 中心模块孪生完整集成，以便与现有设备管理工具轻松集成。


## <a name="do-i-have-to-be-an-azure-iot-customer"></a>是否必须成为 Azure IoT 客户？

是的。 对于云连接的部署，Azure Defender for IoT 依赖于 Azure IoT 连接和基础结构。
## <a name="can-i-create-my-own-alerts"></a>我是否可以创建自己的警报？

是的。 可以对预先确定的行为集（例如 IP 地址和开放端口）设置自定义警报。 请参阅[创建自定义警报](quickstart-create-custom-alerts.md)以详细了解自定义警报以及如何创建它们。

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>可以在何处查看日志？ 是否可以自定义日志？

- 使用连接的 Log Analytics 工作区查看警报和建议。 在该工作区中配置存储大小和持续时间。

- 安全代理中的原始数据也可以存储在 Log Analytics 帐户中。 更改此选项的配置之前，请考虑大小、持续时间、存储要求和关联成本。



## <a name="what-happens-when-the-internet-connection-stops-working"></a>Internet 连接停止工作时会发生什么情况？

只要设备正在运行，传感器和代理便会继续运行并存储数据。 数据根据大小配置存储在安全消息缓存中。 当设备重新连接时，安全消息会恢复发送。

## <a name="next-steps"></a>后续步骤

若要详细了解如何开始使用 Defender for IoT，请参阅以下文章：

- 阅读 Defender for IoT [概述](overview.md)
- 验证 [系统先决条件](quickstart-system-prerequisites.md)
- 了解有关如何开始将[Defender 用于 IoT 的](getting-started.md)详细信息
- 了解 [Defender for IoT 安全警报](concept-security-alerts.md)