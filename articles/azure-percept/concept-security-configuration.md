---
title: Azure Percept 安全文档
description: 了解更多有关 Azure Percept 防火墙配置和安全建议的详细信息
author: mimcco
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 146b39db7aaae2ee043d14d61a7be4f00363c548
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222335"
---
# <a name="azure-percept-security-recommendations"></a>Azure Percept 安全文档

请参阅下面的指南，了解有关如何配置 Azure Percept 防火墙和普遍的安全最佳做法的信息。

## <a name="configuring-firewalls-for-azure-percept-dk"></a>为 Azure Percept DK 配置防火墙

如果网络设置要求显式允许从 Azure Percept DK 设备建立的连接，请查看以下组件列表。

此清单可作为防火墙规则的入手点：

|URL（* = 通配符）|出站 TCP 端口|使用情况|
|-------------------|------------------|---------|
|*.auth.azureperceptdk.azure.net|443|Azure DK SOM 身份验证和授权|
|*.auth.projectsantacruz.azure.net|443|Azure DK SOM 身份验证和授权|

此外，请查看 [Azure IoT Edge 使用的连接](../iot-edge/production-checklist.md#allow-connections-from-iot-edge-devices)的列表。

## <a name="additional-recommendations-for-deployment-to-production"></a>生产部署的其他建议

Azure Percept DK 提供各种现成的安全功能。 除了当前版本中包含的强大的安全功能，Microsoft 还建议在考虑生产部署时遵循以下准则：

- 设备本身具备强大的物理保护
- 确保已启用静态数据加密
- 持续监视设备状况并快速响应警报
- 限制有权访问设备的管理员的数量

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解更多有关 Azure Percept 安全的信息](./overview-percept-security.md)