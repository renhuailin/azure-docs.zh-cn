---
title: 系统先决条件
description: 获取运行 Azure Defender for IoT 所需的系统先决条件。
ms.date: 11/30/2020
ms.topic: quickstart
ms.openlocfilehash: 78d5948554ae531c4b2f77d67bb916d5290db943
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780933"
---
# <a name="system-prerequisites"></a>系统先决条件
本文列出了运行 Azure Defender for IoT 的系统先决条件。

## <a name="minimum-requirements"></a>最低要求

- 支持通过 SPAN 端口监视流量的网络交换机。
- 适用于 NTA 传感器的硬件设备。
- Azure 订阅参与者角色。 只有在加入以定义已提交的设备和与 Azure Sentinel 的连接时才需要。
- Azure IoT 中心（免费或标准层）参与者角色，用于云连接的管理。 请确保已启用 Azure Defender for IoT 功能。
- 对于设备级 Defender-IoT-micro-agent 支持，Defender for IoT 代理支持的设备和平台越来越多。 请参阅[受支持的平台列表](how-to-deploy-agent.md)。

## <a name="supported-service-regions"></a>支持的服务区域

Defender for IoT 将所有欧洲区域的所有流量路由到西欧区域的数据中心。 它将其余所有地区的流量路由到美国中部区域的数据中心。

有关详细信息，请参阅 [IoT 中心支持的区域](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)。

## <a name="see-also"></a>另请参阅

- [确定所需设备](how-to-identify-required-appliances.md)
- [关于 Azure Defender for IoT 网络设置](how-to-set-up-your-network.md)
