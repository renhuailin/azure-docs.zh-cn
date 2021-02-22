---
title: 系统先决条件
description: 获取运行 Azure Defender for IoT 所需的系统先决条件。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 7b609fec2a47dc0685b30dac12f43263127f70ef
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523203"
---
# <a name="system-prerequisites"></a>系统先决条件
本文列出了运行 Azure Defender for IoT 的系统先决条件。

## <a name="minimum-requirements"></a>最低要求

- 支持通过 SPAN 端口监视流量的网络交换机。
- 适用于 NTA 传感器的硬件设备。
- Azure 订阅参与者角色。 只有在加入以定义已提交的设备和与 Azure Sentinel 的连接时才需要。
- Azure IoT 中心（免费或标准层）参与者角色，用于云连接的管理。 请确保已启用 Azure Defender for IoT 功能。
- 对于设备级安全模块支持，Defender for IoT 代理支持的设备和平台越来越多。 请参阅[受支持的平台列表](how-to-deploy-agent.md)。

## <a name="supported-service-regions"></a>支持的服务区域

Defender for IoT 将所有欧洲区域的所有流量路由到西欧区域的数据中心。 它将其余所有地区的流量路由到美国中部区域的数据中心。

有关详细信息，请参阅 [IoT 中心支持的区域](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)。

## <a name="next-steps"></a>后续步骤

[确定所需设备](how-to-identify-required-appliances.md)
[关于 Azure Defender for IoT 网络设置](how-to-set-up-your-network.md)
