---
title: 快速入门：系统必备组件
description: 通过本快速入门，获取运行 Azure Defender for IoT 所需的系统必备组件。
ms.date: 11/30/2020
ms.topic: quickstart
ms.openlocfilehash: f770a6436563439603403e0aedf62d496a38d805
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113011032"
---
# <a name="quickstart-system-prerequisites"></a>快速入门：系统必备组件

本文列出了运行 Azure Defender for IoT 的系统先决条件。

## <a name="prerequisites"></a>先决条件

- 无

## <a name="minimum-requirements"></a>最低要求

- 支持通过 SPAN 端口监视流量的网络交换机。
- 适用于 NTA 传感器的硬件设备。
- Azure 订阅参与者角色。 只有在加入以定义已提交的设备和与 Azure Sentinel 的连接时才需要。
- Azure IoT 中心（免费或标准层）参与者角色，用于云连接的管理。 请确保已启用 Azure Defender for IoT 功能。

## <a name="supported-service-regions"></a>支持的服务区域

Defender for IoT 将所有欧洲区域的所有流量路由到西欧区域的数据中心。 它将其余所有地区的流量路由到美国中部区域的数据中心。

有关详细信息，请参阅 [IoT 中心支持的区域](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [确定所需设备](how-to-identify-required-appliances.md)
