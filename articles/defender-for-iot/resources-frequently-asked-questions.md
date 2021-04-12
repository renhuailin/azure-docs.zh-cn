---
title: Defender for IoT 常见问题解答
description: 查找有关 Azure Defender for IoT 功能和服务的最常见问题的解答。
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 0ce8ded3eea344d72679e0f8b805f45b00279b58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778587"
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

Azure Defender for IoT 可跨所有 IoT/OT 设备提供全面的安全性。 对于最终用户组织，Azure Defender for IoT 提供无代理网络层安全性，可快速部署、使用各种专用的 OT 设备和旧版 Windows 系统，以及与 Azure Sentinel 和其他 SOC 工具互操作。 它可以部署在本地或与 Azure连接的环境中。 对于 IoT 设备生成器，Azure Defender for IoT 提供轻型代理，以将设备层安全性嵌入到新的 IoT/OT 计划中。

## <a name="do-i-have-to-be-an-azure-customer"></a>是否必须成为 Azure 客户？

否，对于 Azure Defender for IoT 的无代理版本，无需成为 Azure 客户。 但是，如果你想要将警报发送到 Azure Sentinel；预配网络传感器，并从云中监视其运行状况；从自动软件和威胁情报更新中获益，你将需要通过 Azure IoT 中心将传感器连接到 Azure。

对于基于代理的 Azure Defender for IoT 版本，你必须是 Azure 客户。

## <a name="can-i-create-my-own-alerts"></a>我是否可以创建自己的警报？

是的，你可以基于多个参数（包括 IP/MAC 地址、协议类型、类、服务、函数、命令等）以及有效负载中包含的自定义标记的值创建自定义警报。  请参阅[创建自定义警报](quickstart-create-custom-alerts.md)，详细了解自定义警报以及如何创建它们。

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Internet 连接停止工作时会发生什么情况？

只要设备正在运行，传感器和代理便会继续运行并存储数据。 数据根据大小配置存储在安全消息缓存中。 当设备重新连接时，安全消息会恢复发送。

## <a name="next-steps"></a>后续步骤

若要详细了解如何开始使用 Defender for IoT，请参阅以下文章：

- 阅读 Defender for IoT [概述](overview.md)
- 验证[系统先决条件](quickstart-system-prerequisites.md)
- 详细了解有关如何[开始使用 Defender for IoT](getting-started.md)
- 了解 [Defender for IoT 安全警报](concept-security-alerts.md)